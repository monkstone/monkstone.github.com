---
layout: post
title: "Processing4 on RaspberryPI4"
date: 2021-10-08 06:00:00
categories: jruby_art update
keywords: picrate, processing, RaspberryPI4, ManjaroARM, RaspbianOS
---

Currently vanilla Processing4 does not support running on the RaspberryPI. Previously processing was restricted to running on Buster (now RaspbianOS) in 32 bit mode. For the future jdk17+ support is likely to be 64 bit mode. Thus there is strong case for using a 64 bit OS on RaspberryPI3 and RaspberryPI4, and I would recommend [ManjaroArm (XFCE)][].

#### Create and run processing sketches in Ruby using PiCrate (approximates to processing4) ####

I've released a [new version][] of [PiCrate][picrate] which works on both RaspberryPI4 and RaspberryPI3B+ with both 32 bit (RaspbianOS) and 64 bit (Manjaro Arm) operating systems. However in the longterm I'm likely to be dropping 32 bit support.

<img src="/assets/steinberg.png">

#### You can also create and run py5 sketches on ManjaroArm (RaspbianOS is untested) ####

See processing [forum][]


[ManjaroArm (XFCE)]:https://manjaro.org/downloads/arm/raspberry-pi-4/arm8-raspberry-pi-4-xfce/
[forum]:https://discourse.processing.org/t/using-geany-ide-for-py5-on-raspberrypi/32236
[new version]:https://discourse.processing.org/t/new-release-of-picrate-for-raspberrypi/32424
[picrate]:https://ruby-processing.github.io/PiCrate/
