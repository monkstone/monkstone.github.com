---
layout: post
title: "OpenGL PiCrate sketches on RaspberryPI"
date: 2018-06-16 05:00:00
categories: jruby_art update
permalink: /picrate_on_raspberrypi/
keywords: processing, jruby, PiCrate, java
---
### This should be straightforward

I originally developed PiCrate on my `amd64` linux box, all looks good (as expected, it is essentially [propane][propane] after-all), and when I got my raspberryPI, I was heady with success when I got `jwishy` etc to run. Not so fast though, opengl sketches are failing.

### Blind alleys

I tried most things, [Gottfreid Haider][haider] warns to use default display settings on PI (this is good advice but did not solve my opengl issue). I even resorted to getting [JRubyArt][jruby_art] working on raspberryPI which was never my intention, but it's there (to do that I used the processing.org raspbian image). I was still stuck when [ShadowfeindX][ShadowfeindX] requested propane to work on Windows, he'd got to work for him (and for opengl he seemed to need non-versioned jars!).

### Light bulb moment

Now it seem to be I have been lulled into false sense of security re `JOGL` and `GLUEGEN` jars, thinking I could use the latest vanilla release (2.3.2), but upon inspection the jars used in vanilla processing have been patched. This wasn't just restricted to the native binary jars, but also removing the versioning seemed to be important.

### The fix

Now this is not super convenient, and messes with my maven/gem build but this what I now do:-

Compile PiCrate (propane) jars as before
Copy and include `JOGL` and `GLUEGEN` jars from vanilla processing into gem

It would have been so much easier if processing.org used maven central.

### The joy

PiCrate 3D now works (in the limited way it can on the RaspberryPI) on the [noobs raspbian distro][noobs]:-

All you need do is install [JRuby][jruby] (it also pays to set up a local gem store). But we are not out of the woods yet [JRuby][jruby] needs fixing (or more specifically its [jnr-ffi][jnr-ffi]) to allow `jgem` install from rubygems, so we still need to download the ArcBall and PiCrate gems and do a local install.

### The proof
monjori shader

![monjori.png]({{site.github.url}}/assets/monjori.png)

joonsrender raytraced media

![balls.png]({{site.github.url}}/assets/balls.png)

Screenshots of opengl frames on raspberryPI don't work (I tried scrot and gimp)

### To Do

Tailor examples for PI, await [jnr-ffi][jnr-ffi] fix (I'm out of my depth with asm types etc). Create some cool PiCrate specific examples such as an implementation of [TechnoMachine][technomachine].

[haider]:https://pi.processing.org/
[technomachine]:https://github.com/mehackit/technomachine
[jnr-ffi]:https://github.com/jnr/jnr-ffi
[jruby]:http://jruby.org/download
[noobs]:https://www.raspberrypi.org/downloads/noobs/
[ShadowfeindX]:https://github.com/ShadowfeindX
[propane]:https://github.com/ruby-processing/propane
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
