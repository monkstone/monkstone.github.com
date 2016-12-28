---
layout: post
title: "Using HProximity and HOscillator from the hype Library"
date: 2016-05-25 13:12:00
categories: jruby_art update
keywords: library, hype, JRubyArt, java8

---

Here is an example sketch by Joshua Davis, from the [hype framework][hype_framework] that has been changed to make use of ruby syntax.
The declared aim of the [hype][hype_library] library is to provide:-
__A collection of classes that performs the heavy lifting so that you can create sketches with the minimum amount of code__. When using the [hype][hype_library] library to create [JRubyArt][jruby_art] sketches it is suggested that you not simply ape the [hype framework][hype_framework] examples, but tailor your sketches to take advantage of the ruby language to create even more elegant code (Code as Art), not that it is relevant here. 

Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.2.1 ide. Note that we can use snake case in place of camel case, for constants use `::` and not `.` to call. The important thing to learn from this sketch is how to implement the `HCallback` interface. This can be implemented as a closure (block), note we do not/should not try and use the vanilla processing method. 

In this case we use the `web_to_color_array` convenience method (since JRubyArt-1.0.7) to create a hash of color int with symbol keys. Note that hype constants require the `H::` prefix, whereas processing constants do not (because we already include the PConstants interface). 

### hype_proximity.rb ###

```ruby

# encoding: utf-8
load_library :hype
include_package 'hype'
java_import 'hype.extended.behavior.HProximity'
java_import 'hype.extended.behavior.HOscillator'

PALETTE = %w(#242424 #00FF00 #FF3300 #4D4D4D).freeze
KEY = %i(background fill_one fill_two stroke).freeze

attr_reader :colors

def settings
  size(640, 640)
end

def setup
  sketch_title 'Hype Proximity'
  @colors = KEY.zip(web_to_color_array(PALETTE)).to_h
  H.init(self)
  H.background(colors[:background])
  canvas = H.add(HCanvas.new.auto_clear(false).fade(5))
  r1 = HRect.new(5)
  r1.no_stroke
    .fill(colors[:fill_one])
    .loc(width/2, height/2)
    .rotate(45)
    .anchor_at(H::CENTER)
  canvas.add(r1)
  r2 = HRect.new(5, 10)
            .rounding(4)
  r2.no_stroke
    .fill(colors[:fill_two])
    .x(40)
    .y(height / 2)
    .anchor_at(H::CENTER)
  canvas.add(r2)
  HProximity.new
            .target(r2)
            .neighbor(r1)
            .property(H::HEIGHT)
            .spring(0.99)
            .ease(0.7)
            .min(10)
            .max(200)
            .radius(250)
  HOscillator.new
             .target(r2)
             .property(H::X)
             .range(40, 600)
             .speed(5)
             .freq(0.5)
             .current_step(-180)
end

def draw
  H.draw_stage
  # outline to show area of proximity  
  ellipse_mode(CENTER)
  stroke(colors[:stroke])
  no_fill
  ellipse(width / 2, height / 2, 500, 500)
end
```

<img src="/assets/proximity.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/