---
layout: post
title: Vero 4K + review
author: Martin Rotter
date: 2019-01-30 08:00:00 +0200
category: it-programming
language: en
imgfolder: /assets/2019/01/vero-4k
images:
  - name: 1.jpg
  - name: 2.jpg
  - name: 3.jpg
  - name: 4.jpg
  - name: 5.jpg
  - name: 6.jpg
  - name: 7.jpg
  - name: 8.jpg
---

Some time ago, I moved to a new house, bought 4K-capable SDR "non-smart" display and I needed mini PC for hassle-free 4K SDR audio/video playback. After some browsing I stumbled upon [Vero 4K +](https://osmc.tv/vero) and bought it. You can see [all photos](#){: id="gall1"} attached to this article.
<!--more-->

[![Vero 4K + box]({{ page.imgfolder }}/4.jpg){: width="400px" }]({{ page.imgfolder }}/4.jpg){: .swipebox .image-border}

## Packaging & Shipment

I order my package in the middle of January, 2019, when it was re-stocked and sale for discount price was started. Process of ordering the Vero was smooth.

Some problems arose as I expected them to ship the Vero fast, as is written on their [website](https://osmc.tv/) where they claim the shipping is "Free and fast". Unfortunately, it turned out that the interest in the sale was massive and dispatch of Vero was rather slow and it actually took them several days to send the item.

When package was handed out to Royal Mail and consecutively transferred via GLS, it took just two days to deliver the package to Czech Republic, which is amazingly fast deliverance.

[![Packaging]({{ page.imgfolder }}/3.jpg){: width="400px" }]({{ page.imgfolder }}/3.jpg){: .swipebox .image-border}

I have to say that packaging was perfect. Nothing was damaged and the package had all the goods in it as was advertised:
* Vero 4K +,
* remote controller + USB remote receiver,
* HDMI cable,
* power adapter (5V/2A),
* mounting plate,
* IR extender.

[![Package contents]({{ page.imgfolder }}/5.jpg){: width="400px" }]({{ page.imgfolder }}/5.jpg){: .swipebox .image-border}

## Build quality

Overally, Vero 4K + seems to be a pretty well-designed piece of HW.

[![Vero 4K + box]({{ page.imgfolder }}/8.jpg){: width="400px" }]({{ page.imgfolder }}/8.jpg){: .swipebox .image-border}

Vero is quite small, you can easily hide it behing your TV. Plastic case is quite stiff and does not have cheapish looking. It just looks solid and and its edges are relatively sharp, do not expect any rounded edges or corners. Vero offers modern look and will look great when placed on proper place in your living room.

Vero offers a bunch of external ports, including USB 2.0, HDMI or Gigabyt ethernet. I have to say that all ports are well-built and all respectable connectors I tried fit well. Nothing wiggles, all cables sit exactly where I plug them, everything is firmly seated. Sometimes too firmly, because I had quite a difficult time when I wanted to unplug USB remote receiver from Vero's USB port. I had to pull really hard to get the thing out!

Also, the two USB ports are placed too close to themselves and sometimes you might have a real trouble when plugging in two wide USB devices (for example some USB flash drives which have big casing).

OSMC remote, on the other hand, is perfect. It fits very well in my small hand, it is light and it is stylish. It simply does its job and is enough if you want to control Kodi - navigate its menus, play stuff and so on.

Only possible thing the remote lacks is embedded mini keyboard, but internet is full of cheap RF remotes which offer mini keyboard and even a touchpad. I consider buying one later.

## Software equipment

While Vero is almost perfect regarding its HW, SW side of things have some drawbacks.

When you power one your Vero for the first time, then you are greeted with nice and fancy wizard which will guide you throught initial Vero setup. Wizard itself did its job and everything was flawless. Well, until first "sad smile BSOD" appeared. I do not exactly remember which page of wizard it happened on, but I think it was on "Network setup" page. BSOD screen went away after a few seconds and main Kodi screen displayed on screen.

Vero uses [OSMC](https://osmc.tv), which is special [GNU/Linux](https://www.debian.org/releases/stable/mips/ch01s02.html) distribution based on [Debian](https://www.debian.org).

It is nice that OSMC is based on fully featured operating system with availability of tons SW packages, which makes it better in this regard than [LibreELEC](https://libreelec.tv) which is more lightweight and strictly driven, as it is "just-enough OS for Kodi".

In other words, it is more difficult to install additional SW into LibreELEC than into OSMC.

It is easy to install updates to OSMC instance because it comes with "My OSMC" app which offers simple GUI to install updates with a few remote clicks.

OSMC also offers terminal utility `apt` (because it is based on Debian) so that you can even install updates on command line which is a thing which majority of advanced Linux users will prefer because you usually have more control in the command line than you have in GUI.

That said, sequence of command for updating all packages in OSMC is suprisingly:

```bash
sudo apt-get update
sudo apt-get dist-upgrade
```

So not `sudo apt-get upgrade` as all Linux geeks might expect and this is really weird. Even [wiki](https://osmc.tv/wiki/general/keeping-your-osmc-system-up-to-date) page says that running `sudo apt-get upgrade` is "dangerous" and should be avoided. Well, this is just weird and personally I do not understand which they prefer `dist-upgrade` instead. It just seems OSMC creators decided to go the "easy" way as `dist-upgrade` is able to update all your packages even if the process requires additional installation or removel of some other dependent packages. So, when you run `dist-upgrad`, then updateable packages will indeed get updated, but some other packages may be uninstalled in the process as well, which is relatively dangerous as it may turn out that you actually used some of packages which were deleted in the process and that's way simple `upgrade` command is prefered as its purpose is to upgrade only packages which can be actually updated without removing or installing something else.

That said, I would understand why they recommend using `dist-upgrade` but do not get why they warn users of supposedly safer `upgrade`. One possible explanation could be that they actually want users to avoid doing ["partial upgrades"](https://wiki.archlinux.org/index.php/System_maintenance#Partial_upgrades_are_unsupported).

Other thing is the when playing with menus/options/switches, Kodi crashed several times within and hour and presented BSOD to me. If these issues are still a thing when I move the Vero to my new house, I will file a bug reports and try to investigate these crashes.

## Video playback

I currently own non-HDR IPS screen which runs at 2560 ⨯ 1440 screen size and therefore does not natively run at 4K. That said I was not expecting to play 4K content at all with this screen.

So I tried some high bitrate 1080p (FullHD) [sample files](http://jell.yfish.us) and played them from USB flash drive. I have to say that results were astonishing as all 1080p movies of many distinct formats and containers played well. Nothing was laggy, nothing broke. Every audio/video file played nicely and I was amazed by colors I saw.

At that point I decided to try to play 4K 60FPS HDR content. I downloaded some [samples](http://jell.yfish.us) and some extra videos frou Youtube, transferred them to USB flash drive and played them one by one.

And to may surprise, all 4K videos played very nice and smooth. I even check the "information OSD" and it really said that those 4K videos are playing in 4K resolution, which is just weird, because the used monitor itself does not support it! I suspect Vero did some magic in the background or perhaps the monitor was able to downscale video content, but I am not really sure what happened. Anway 4K played really really nice and even HDR content looked suprisingly well on SDR screen, although some of them might need little adjustments to contrast/gamma/brightness.

I have even tried to run 8K videos (just for fun) and when I tried to play it, whole box froze and I was not able to revive it. In the end, I had to plug out/in power cable to reboot the device.

### Youtube

I tried to also play some videos via official Kodi Youtube addon, but the addon seems to have some bugs, and probably does not support 4K videos yet, because I was not able to make it run, neither with "inputstream adaptive" nor without it. The max resolution I was able to get, was 1080p, when I manually switched video stream after the video playback started.

## Other thoughts

What to say. Vero is nice piece of HW and it is able to play all major video/audio formats without any problems and that's its main purpose.

Of course there are some minor annoyances or weird things (from to POV of advanced Linux user/admin), but overally these kind of things will not bother regular users as Vero offers flawless plug-n-play experience for them and is probably one of best devices in this regard on the market.