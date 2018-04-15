---
layout: post
title: "Turning Problems Into Opportunities"
date: 2018-04-15 06:00:00
categories: jruby_art update
permalink: /opportunity/
keywords: opportunities, processing, propane, developer, jdk9
---
### Problems and Opportunities

There was an ethos in my last job that you should turn problems into opportunities, it was bit of Orwelian [doublespeak][doublethink] really, but sometimes you can make it work for you (and not just putting a positive spin on a disaster). A classic example make a cock-up on a project for Japanese clients, then you find out they can't lose face because they feel bad about too (after all they selected you to the job and so feel responsible). Of course if you can come up with fix they'll think you are excellent. But there really are opportunities sometimes, and so it is with some recent issues thrown up by jdk9 changes.

Propane and JRubyArt rely on calling an [inherited `protected`][protected] java method, and currently this is not possible (JRuby guys could potentially produce a fix, but are reluctant to do so as it would potential expose `protected` java methods from non inherited classes). The opportunity here is that I can fairly easily produce a fix by compiling my own custom processing-core, and in doing so introduce custom icons to display in OS panel. There are other benefits as I will not have to wait for the processing guys to produce other fixes (required for MacOS), however I will be looking for [contributors][developing] for help here (being a strictly linux person).


[developing]:{{ github.site.url }}/developing/
[protected]:{{ github.site.url }}/jdk9/
[doublethink]:https://en.wikipedia.org/wiki/Doublespeak
[vanilla]:https://github.com/processing/processing/wiki/Supported-Platforms#java-9
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
[propane]:https://ruby-processing.github.io/propane/
[core]:https://github.com/ruby-processing/processing-core
