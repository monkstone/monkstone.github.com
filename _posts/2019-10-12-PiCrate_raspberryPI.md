---
layout: post
title: "Picrate-1.0 Raspbian Stretch"
date: 2019-10-12 06:00:00
categories: jruby_art update
keywords: jruby, picrate, processing, RaspberryPI, Oracle, java
---

I have just released what will probably be the final version of PiCrate for rasbian stretch on RaspberryPI3+. It is specifically designed to use the Oracle jdk8 that comes with the stretch distribution, here is a snapshot of sketch running from the Geany IDE:-

![stretch.png]({{site.github.url}}/assets/stretch.png)

For buster and later I expect to be using OpenJDK, but currently it seems to lack performance, I have already create a [jdk11 branch][branch] to support this work.


[branch]:https://github.com/ruby-processing/PiCrate/tree/jdk11
