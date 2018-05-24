---
layout: post
title: "Processing sketches on RaspberryPI with JRuby"
date: 2018-05-24 06:00:00
categories: jruby_art update
permalink: /picrate_on_raspberrypi/
keywords: processing, jruby, PiCrate, java
---


### A sketch demonstrating ruby support for rational numbers

![fraction_sums.png]({{site.github.url}}/assets/fraction_sums.png)

### A Sketch demonstrating ruby support for complex numbers

![mandelbrot.png]({{site.github.url}}/assets/mandelbrot.png)

What this sketch demonstrates is that you can tap into a java class. It is possible via duck-typing to create (with no ceremony) a Vec2D instance from a PVector, it requires more ceremony but it is possible to create a PVector instance from Vec2D instance. For some reason PVector to String returns an array of java float.

[propane]:https://ruby-processing.github.io/propane/
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
