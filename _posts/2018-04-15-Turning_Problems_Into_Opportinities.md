---
layout: post
title: "Turning Problems Into Opportunities"
date: 2018-04-15 06:00:00
categories: jruby_art update
permalink: /opportunity/
keywords: opportunities, processing, propane, developer, jdk9
---
### Actual and anticipated problems ###

When I first tried to run JRubyArt and propane with jdk9, neither would run owing to changes in the security model for jdk9 (_which actually is only a development model, as will be jdk10 leading to jdk11 a LTS version from Oracle scheduled for September 2018 release_). LTS is a moot point with Oracle, as only paying customers will get LTS beyond 6 months, but that's what the processing.org guys are waiting for, I suggest it is time to change to OpenJDK, especially after the débâcle with the Google patent case. I was reluctant to wait to September to see if there will be a future for my projects so I investigated the problem, and it turned out to be a limitation with JRuby and jdk9 (both Oracle and OpenJDK versions) not able to call `protected` methods on an inherited class, as one can in pure java. However other changes introduced in jdk9 (but not 100% implemented yet) will equally affect vanilla processing as well, and affect the ability to use proprietary code eg `com.sun.glass.ui.Screen.getRenderScale()` used by vanilla processing in `PSurfaceFX.java`. This limitation will likely also affect vanilla processing hacks to provide retina support etc that use using the `apple.jar`.

### Opportunities ###

There was an ethos in my last job that you should turn problems into opportunities, it was bit of Orwelian [doublespeak][doublethink] really, but sometimes you can make it work for you (and not just putting a positive spin on a disaster). A classic example make a cock-up on a project for Japanese clients, then you find out they can't lose face because they feel bad about it too (after all they selected you to do the job and so feel responsible). Of course if you can come up with fix they'll think you are excellent. But there really are opportunities sometimes, and so it is with some recent issues thrown up by jdk9 changes.

Propane and JRubyArt rely on calling an [inherited `protected`][protected] java method, and currently this is not possible (JRuby guys could potentially produce a fix, but are reluctant to do so as it would potential expose `protected` java methods from non inherited classes). The opportunity here is that I can fairly easily produce a fix by compiling my own custom processing-core, and in doing so introduce custom icons to display in OS panel. There are other benefits as I will not have to wait for the processing guys to produce other fixes (required for MacOS, owing to the hacks they use to cater for retina screens depending on proprietary code). For MacOS fixes I will however will be looking for [contributors][developing] for help here (being a strictly linux person).

Only by creating a fix for the critical problem could I go on to test the rest of propane, it seems that it mostly just works, with the exception of FX2D and FX3D sketches (best avoided at present and unless javaFX improves I would say in future too). However the JRubyArt and propane `control_panel` library would not work. It transpired that this was just another case of calling an [inherited `protected`][protected] java method `fireStateChanged`, but this created an opportunity to take fresh look at the `control_panel` library, which I had not touched in years.  The refactored library, not only works, but apart from a minor issue with initially labelling sliders (with their value) now requires less boilerplate code in sketches (win/win).

### Call for help ###

It seem likely for the future of processing on ruby will be the [propane][propane] project. This relies on an installed JRuby _cf_ the option of using JRuby-complete in ruby-processing and JRubyArt, there may be people interested in developing a similar option for propane? Any MacOS specific development/testing will depend on contributors as will any Windows or raspberry-pi versions.


[developing]:{{ github.site.url }}/developing/
[protected]:{{ github.site.url }}/jdk9/
[doublethink]:https://en.wikipedia.org/wiki/Doublespeak
[vanilla]:https://github.com/processing/processing/wiki/Supported-Platforms#java-9
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
[propane]:https://ruby-processing.github.io/propane/
[core]:https://github.com/ruby-processing/processing-core
