---
title: "Showing Settings from macOS Menu Bar Items: A 5-Hour Journey"
description: "Why something as simple as showing a settings dialog from a macOS menu bar app took me 5 hours to figure out, and requires 50 lines of code for what should be a one-liner."
pubDatetime: 2025-06-17T02:00:00.000+01:00
heroImage: /assets/img/2025/showing-settings-from-macos-menu-bar-items/hero.png
heroImageAlt: "macOS Settings window appearing from a menu bar app"
tags:
  - macOS
  - SwiftUI
  - MenuBarExtra
  - Settings
draft: false
---

Opening a settings window from a macOS menu bar app should be trivial. It's not. After spending hours debugging, I'm documenting the gotchas to save you the same frustration.

## The Problem

SwiftUI provides [`SettingsLink`](https://developer.apple.com/documentation/swiftui/settingslink) for opening settings:

```swift
MenuBarExtra("Test", systemImage: "star.fill") {
    SettingsLink {
        Text("Open settings") 
    }
}
```
Simple, right? Except it doesn't work reliably in [`MenuBarExtra`](https://developer.apple.com/documentation/swiftui/menubarextra). The documentation doesn't mention this limitation.

According to Apple's documentation, `SettingsLink` should "open the app's settings scene when activated." However, this assumes your app is already active and has proper window management context - assumptions that don't hold for menu bar apps.

## Why It Fails

Menu bar apps operate differently from regular macOS apps:
- **No dock icon by default** - They use [`NSApplication.ActivationPolicy.accessory`](https://developer.apple.com/documentation/appkit/nsapplication/activationpolicy/accessory)
- **Not "active" in the traditional sense** - They don't appear in the app switcher
- **Windows may appear behind other apps** - Without proper activation, windows lack focus
- **No SwiftUI graph loaded** - Settings needs some SwiftUI view initalized and the menu bar uses AppKit under the hood.

The root issue is that [`NSApplication`](https://developer.apple.com/documentation/appkit/nsapplication) treats menu bar apps as background utilities, not foreground applications. This affects how windows are ordered and receive events.

## The Evolution of Workarounds

### The Old Way

This is how it used to work. Private API, but widely used and simple:

```swift
if #available(macOS 13, *) {
    NSApp.sendAction(Selector(("showSettingsWindow:")), to: nil, from: nil)
} else {
    // macOS 12 or earlier
    NSApp.sendAction(Selector(("showPreferencesWindow:")), to: nil, from: nil)
}
```

This stopped working in Sonoma (14) with the error: "Please use SettingsLink for opening the Settings scene." Apple deprecated these selectors in favor of SwiftUI's scene-based approach, but didn't account for the unique challenges of menu bar apps.

### The openSettings Environment Action

Apple provides an [`openSettings` environment action](https://developer.apple.com/documentation/swiftui/opensettingsaction) for programmatic access (available since macOS 14.0+):

```swift
struct MyView: View {
    @Environment(\.openSettings) private var openSettings

    var body: some View {
        Button("Open Settings") {
            openSettings()
        }
    }
}
```
This currently works on macOS 15, but doesn't work on macOS Tahoe (26). The logic needs an existing SwiftUI render tree, and simply calling the environment variable does nothing if none is found. 

The workaround? As horrible as it sounds, a *hidden window*. Of course, that comes with its own issues, unless you massage the window that it's really off-screen and ideally also doesn't react to touches.

## Hide & Seek

Now, this works, however the window will open in the *background*, and no amount of `makeKeyAndOrderFront(nil)` will help. Trust me. I (and [Claude](/posts/2025/claude-code-is-my-computer/)) tried plenty variations.

The real reason? macOS doesn't allow a window to become selected when there's no Dock icon. And since it's common to hide the Dock icon for pure Menu Bar apps, that's a problem.

The workaround? Show the Dock icon just before calling `openSettings()` and then hiding it again. In a way, this is also convenient for the user as the Icon now represents the "app" - the visible window, and once that closes, we hide the Dock icon again. (via calling `NSApp.setActivationPolicy(.accessory)`). Of course the whole thing requires some delays to really work, so let me present you the final, working solution (I apologize in advance):

## The Working Solution

Here's the minimal implementation for macOS 14 and higher, using Swift 6:

```swift
// Hidden window to provide context
struct HiddenWindowView: View {
    @Environment(\.openSettings) private var openSettings
    
    var body: some View {
        Color.clear
            .frame(width: 1, height: 1)
            .onReceive(NotificationCenter.default.publisher(for: .openSettingsRequest)) { _ in
                Task { @MainActor in
                    // Show dock icon for window focus
                    NSApp.setActivationPolicy(.regular)
                    try? await Task.sleep(for: .milliseconds(100))
                    
                    // Activate and open
                    NSApp.activate(ignoringOtherApps: true)
                    openSettings()
                }
            }
            .onReceive(NotificationCenter.default.publisher(for: .settingsWindowClosed)) { _ in
                // Restore menu bar app state when settings closes
                NSApp.setActivationPolicy(.accessory)
            }
    }
}

// App structure
@main
struct MenuBarApp: App {
    var body: some Scene {
        MenuBarExtra("My App", systemImage: "star.fill") {
            Button("Settings...") {
                NotificationCenter.default.post(name: .openSettingsRequest, object: nil)
            }
            .keyboardShortcut(",", modifiers: .command)
        }
        
        // Required Settings scene
        Settings {
            SettingsView()
                .onDisappear {
                    NotificationCenter.default.post(name: .settingsWindowClosed, object: nil)
                }
        }
        
        // Hidden window for context
        Window("Hidden", id: "HiddenWindow") {
            HiddenWindowView()
        }
        .windowResizability(.contentSize)
        .defaultSize(width: 1, height: 1)
    }
}

extension Notification.Name {
    static let openSettingsRequest = Notification.Name("openSettingsRequest")
    static let settingsWindowClosed = Notification.Name("settingsWindowClosed")
}
```

The [`NotificationCenter`](https://developer.apple.com/documentation/foundation/notificationcenter) approach decouples the menu action from the window context, allowing the hidden window to handle the actual settings opening.

For a production-ready implementation with all edge cases (yes, there are some more...) handled, see [VibeTunnel's SettingsOpener.swift](https://github.com/amantus-ai/vibetunnel/blob/main/VibeTunnel/Utilities/SettingsOpener.swift).

## Understanding the Workaround

The hidden window serves multiple purposes:
- Provides a valid window context for the `openSettings` environment action
- Allows us to temporarily switch activation policies without visual disruption
- Gives us a place to handle the complex timing orchestration

The dock icon manipulation (switching between [`.accessory`](https://developer.apple.com/documentation/appkit/nsapplication/activationpolicy/accessory) and [`.regular`](https://developer.apple.com/documentation/appkit/nsapplication/activationpolicy/regular)) is necessary because macOS only brings windows to the front reliably for apps with dock icons.

## Fin

What should be a one-liner in other frameworks requires careful orchestration in SwiftUI. The combination of [`MenuBarExtra`](https://developer.apple.com/documentation/swiftui/menubarextra), [`Settings`](https://developer.apple.com/documentation/swiftui/settings) scenes, and [`openSettings`](https://developer.apple.com/documentation/swiftui/opensettingsaction) wasn't designed with the unique constraints of menu bar apps in mind.

This shouldn't be so hard. Opening a settings window is one of the most basic operations any app needs to perform. The fact that it requires hidden windows, activation policy juggling, and precise timing delays in 2025 is a testament to how menu bar apps remain second-class citizens in SwiftUI. Until Apple addresses these fundamental issues, we're stuck with these workarounds.