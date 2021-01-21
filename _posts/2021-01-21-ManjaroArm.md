---
layout: post
title: "Processing4 on RaspberryPI4"
date: 2021-01-21 06:00:00
categories: jruby_art update
keywords: picrate, processing, RaspberryPI4
---

Recently I've released a new version of [PiCrate][picrate] which works on both RaspberryPI4 and RaspberryPI3B+ with both 32 bit (RaspbianOS) and 64 bit (Manjaro Arm) operating systems. So I know it should be possible to run vanilla processing4 on the RaspberryPI. Now I've previously shown on the [processing forum][forum] how to get Sam Pottingers Alpha releases of vanilla processing4 on the raspberryPI. This is not possible with the latest-4.0a3 release, there seems to be a Catch 22 because when you try to run a sketch on Manjaro Linux it complains that core needs to run on a 32 bit OS, yet the jogl aarch64-linux binaries are included.
Anyway you may need to update the symbolic link on manjaro linux if you are using the stock openjdk which has been just updated to jdk-15 from jdk14. Here's an example sketch running with updated jdk:-

<img src="assets/Processing_On_Manjaro_Linux.jpg">



[forum]:https://discourse.processing.org/t/processing-in-style-with-java-11/13776/46
[picrate]:https://ruby-processing.github.io/PiCrate/
