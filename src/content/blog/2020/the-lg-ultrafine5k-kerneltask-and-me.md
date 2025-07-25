---
title: "The LG UltraFine 5K, kernel_task, and Me"
pubDatetime: 2020-05-19T06:00:00.000Z
description: "A four-year saga with the problematic LG UltraFine 5K display and the surprising discovery that plugging it into the wrong MacBook side causes performance issues."
heroImage: /assets/img/2020/appleintelframebuffer/lg-box.jpg
tags:
  - Apple
  - Hardware
  - macOS
  - Troubleshooting
  - LG-UltraFine
  - MacBook-Pro
  - Thunderbolt
  - Display
  - Thermal-Management
  - kernel_task
source: steipete.com
AIDescription: true
---

A good story is nuanced and complicated, and it contains surprise twists and a happy ending. Me owning an LG UltraFine 5K delivers on all of that. So let’s dive right in:

{% twitter https://twitter.com/steipete/status/1253550223445708800 %}

## Background

I own one of the [cursed](https://mjtsai.com/blog/2020/02/03/macos-display-problems/) LG UltraFine 5K displays.[^1] In fact, I don’t just own one; I own about 10 of them. But I realize now that blindly trusting in Apple hardware is a mistake.

<blockquote class="instagram-media" data-instgrm-permalink="https://www.instagram.com/p/BPuxofmASSQ/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="12" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/p/BPuxofmASSQ/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto 12px; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.899 532.1,66.113 541,66.113 C549.9,66.113 557.115,58.899 557.115,50 C557.115,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.022 564.756,66.606 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.607,73.757 556.021,74.244 553.102,74.378 C549.944,74.521 548.997,74.552 541,74.552 C533.003,74.552 532.056,74.521 528.898,74.378 C525.979,74.244 524.393,73.757 523.338,73.347 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.606 516.755,65.022 516.623,62.101 C516.479,58.943 516.448,57.996 516.448,50 C516.448,42.003 516.479,41.056 516.623,37.899 C516.755,34.978 517.244,33.391 517.654,32.338 C518.197,30.938 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.393,26.244 525.979,25.756 528.898,25.623 C532.057,25.479 533.004,25.448 541,25.448 C548.997,25.448 549.943,25.479 553.102,25.623 C556.021,25.756 557.607,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.938 564.346,32.338 C564.756,33.391 565.244,34.978 565.378,37.899 C565.522,41.056 565.552,42.003 565.552,50 C565.552,57.996 565.522,58.943 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C550.169,20.033 549.148,20 541,20 C532.853,20 531.831,20.033 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.035,40.831 511,41.851 511,50 C511,58.147 511.035,59.17 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C531.831,79.965 532.853,80.001 541,80.001 C549.148,80.001 550.169,79.965 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.966,59.17 571,58.147 571,50 C571,41.851 570.966,40.831 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;"> View this post on Instagram</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 20px; width: 20px;"></div> <div style=" width: 0; height: 0; border-top: 2px solid transparent; border-left: 6px solid #f4f4f4; border-bottom: 2px solid transparent; transform: translateX(16px) translateY(-4px) rotate(30deg)"></div></div><div style="margin-left: auto;"> <div style=" width: 0px; border-top: 8px solid #F4F4F4; border-right: 8px solid transparent; transform: translateY(16px);"></div> <div style=" background-color: #F4F4F4; flex-grow: 0; height: 12px; width: 16px; transform: translateY(-4px);"></div> <div style=" width: 0; height: 0; border-top: 8px solid #F4F4F4; border-left: 8px solid transparent; transform: translateY(-4px) translateX(8px);"></div></div></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center; margin-bottom: 24px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 224px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 144px;"></div></div></a><p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;"><a href="https://www.instagram.com/p/BPuxofmASSQ/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none;" target="_blank">A post shared by PSPDFKit (@pspdfkit)</a> on <time style=" font-family:Arial,sans-serif; font-size:14px; line-height:17px;" datetime="2017-01-26T14:25:55+00:00">Jan 26, 2017 at 6:25am PST</time></p></div></blockquote> <script async src="//www.instagram.com/embed.js"></script>

## Problems

Since we first purchased the displays, we’ve been having issues with them, starting with [delayed shipping](https://mjtsai.com/blog/2016/12/20/lg-5k-ultrafine-display-delayed/), [ghosting](https://www.reddit.com/r/mac/comments/a2vf8i/lg_5k_ultrafine_ghosting/), and [Wi-Fi interference](https://www.macrumors.com/2017/03/15/lg-ultrafine-5k-shielding-fixed/), and moving on to compatibility — there’s no HDMI, DisplayPort, or similar. The only way to use these monitors is with a modern Mac.[^2] As for the compatibility issue, that was a known tradeoff, and for me, it was acceptable. After all, the benefit of only having a single cable as a modern docking station and a beautiful panel outweighed the drawbacks. I still remember [my innocent excitement](https://twitter.com/steipete/status/819869709294325760).

Since receiving these displays, we’ve had to return most of them to get fixes for various issues, and we’ve patiently updated the firmware multiple times with [LG’s crappy Screen Manager](https://twitter.com/steipete/status/915141641308172288) software. There are also [issues with expanding batteries, and Apple has blamed the LG 5K](https://twitter.com/steipete/status/1232654186598281216), saying just don’t use it a lot and you’ll be UltraFine.

## The Great Flickering

With the 2017 MacBook Pro, I had an extraordinary amount of fun, since plugging in the LG was causing graphic issues on the MacBook display. I wrote radars, called Apple Support, and even took a cab to a repair center with the screen in tow in order to prove the hardware was broken. It’s difficult when neither the screen itself nor the MacBook have issues, but the combination of the two causes problems.

{% twitter https://twitter.com/steipete/status/956863946404827136 %}

Nobody knew what was going on, and this all occurred before we had an Apple Store in Austria, so getting help there was out of the question. Apple eventually agreed to change my logic board for free, but only after countless hours of phone calls and emails and after this issue had been escalated multiple times. Mind you, this was all with active AppleCare and a machine that wasn’t even one year old. The replacement of my logic board took more than a week, and after it was returned, it had the exact same issue. I mostly gave up and just didn’t use my external screen.

## kernel_task Goes Omnomnom

The graphic issues disappeared with the 2018 MacBook Pro. However, the biggest issue was also the weirdest: Sometimes, after longer use, my MacBook Pro became unusably slow. Mere seconds after removing the external screen, kernel_task disappeared back into the CPU activity ground noise. Sometimes I had hours after disconnecting in which the MacBook worked, but other times this happened really fast. (I use [iStat Menus](https://bjango.com/mac/istatmenus/) here.)

{% twitter https://twitter.com/steipete/status/1128703168697839617 %}

I was never able to reliably reproduce this, nor could Apple Support help. They also claimed to not know anything about this issue. I again mostly gave up and didn’t use the external screen.

## 16-Inch MacBook Pro and LG UltraFine

I spend between eight and ten hours per day on my MacBook Pro, so updating it every year is a good investment. Of course, this also gave me the opportunity to test each hardware generation with the LG display.

When Apple announced the 16-inch MacBook Pro, I just had to get one. I don’t enjoy splitting my work across multiple machines, and I like to move around in my apartment, so it had to be a portable one. Over the years, Apple has managed to build beefier and beefier portables. However, its 8-core 15-inch MacBook Pro is severely thermal throttled — [using this tool](https://software.intel.com/content/www/us/en/develop/articles/intel-power-gadget.html), you can see how it runs at top speed for a few seconds, only to be thermal throttled.

The 16-inch MacBook Pro was marketed as “[thicker](https://www.theverge.com/2019/11/18/20971297/macbook-pro-16-inch-battery-apple-thickness-teardown-ifixit)” as a way of fixing the throttling, and it delivered! Everything is noticeably faster. It also fixed my biggest issue with the whole MacBook Pro lineup so far: the dreaded kernel_task that eats up CPU like Pac-Man eats ghosts.

Then, in April, I stumbled upon a [Stack Exchange question](https://apple.stackexchange.com/questions/363337/how-to-find-cause-of-high-kernel-task-cpu-usage) that seemed to explain what was going on all these years.

> high CPU usage by kernel_task is caused by high Thunderbolt Left Proximity temperature, which is caused by charging and having normal peripherals plugged in at the same time.

I HAD PLUGGED THE SCREEN INTO THE WRONG SIDE. YES.

The 16-inch MacBook Pro doesn’t seem to suffer from this temperature sensor misplacement and can drive the LG UltraFine without slowdown on both ends. But all generations before have this issue (tested with 2016 and later, purchased every generation.) [My posting about the issue on Twitter made waves, and I even ended up being mentioned in some tech articles](https://www.trustedreviews.com/news/is-there-really-a-wrong-way-to-charge-a-macbook-pro-4026796).

The bad news: The LG can provide 87 watts of power, but the notebook comes with a 96-watt adaptor. This means that the battery is constantly compensating. Play StarCraft for three hours and your computer will shut off with a dead battery. Even when compiling code in Xcode, the battery will fluctuate. To deal with this, [Apple recommends using a separate power adapter](https://twitter.com/BesherMaleh/status/1206434150078656512) when using the 16-inch MacBook Pro.

## Brightness Bugs

Though the above issue was solved, there is a new bug that started happening with the 16-inch MacBook Pro: Sometimes after wakeup, the gamma settings of the screen are way off (usually too bright, but it can also be [too dark](https://twitter.com/tkanzakic/status/1263345836538421248?s=21)). This can be fixed via unplugging/replugging or via [toggling True Tone](https://twitter.com/nicholascooke/status/1263244851266510848?s=21). This bug is [independent](https://twitter.com/sdw/status/1263226044435140608?s=21) [of](https://twitter.com/alihilal94/status/1263227224217595904?s=21) [the](https://twitter.com/oleg_v_soloviev/status/1263314894918729735?s=21) [screen](https://twitter.com/nicholascooke/status/1263244851266510848?s=21) you use though. (Apple: FB7722012) Also, don’t confuse this bug with the [eye-burning brightness bug after reboot](https://macperformanceguide.com/blog/2020/20200107_1436-2019MacPro-LG5K-maximum-brightness-after-reboot.html) (I know it’s hard to keep up with all the different bugs these days). Luckily, Apple fixed the latter bug in Catalina 10.15.4.

{% twitter https://twitter.com/steipete/status/1263225822485381120 %}

## Conclusion

Do I recommend this setup? Yes. I am writing this story on the LG display after all. I mostly use the separate power plug to fix the “missing 9-watt problem.” If you have top-notch hardware, it is a beautiful panel, and it seems Apple has fixed all the issues after a four-year hiatus. (And by fixed, I mean they released new hardware, aka [Fix In Next Chip](https://twitter.com/blelbach/status/1258680434432458752).) Besides, every time I [look around for other screens](https://mjtsai.com/blog/2019/06/26/the-great-monitor-search-continues/), they all seem terrible.

And it’s true: Once you are used to Retina, you don’t want to go back to 4K. Maybe this is all a years-long elaborate ploy to make me buy a [Pro Display XDR](https://www.apple.com/pro-display-xdr/). Maybe I am just a [bug magnet](https://twitter.com/steipete/status/1253979468164673536). Or maybe it’s [not just me](https://mjtsai.com/blog/2017/01/09/lg-ultrafine-5k-reviews/).

Is this the end of the story? Find out in [Kernel Panics and Surprise boot-args](https://steipete.com/posts/kernel-panic-surprise-boot-args/).

Update: LG released [an update to the 5K Display](http://www.lgnewsroom.com/2019/07/lg-introduces-new-ultrafine-5k-display-2/) that increases power output to 94 watts.

[^1]: LG released [a second generation](https://9to5mac.com/2019/07/30/new-lg-ultrafine-5k-display/) of the LG UltraFine 5K display, and it adds support for 3840 x 2160 @ 60Hz over USB-C DisplayPort, so you can now connect an iPad Pro. Sporadic reports also mention fewer issues with the second generation.

[^2]: I managed [to get it working](https://www.reddit.com/r/hackintosh/comments/ae8d6c/is_it_possible_to_build_a_hackintosh_that/) with a Gigabyte GC-Titan Ridge card on a Hackintosh.
