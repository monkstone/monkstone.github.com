---
layout: post
title: "Using nested callbacks hype Library"
date: 2016-04-23 11:40:00
categories: jruby_art update
keywords: library, hype, JRubyArt, java8, lambda
permalink: nested_callbacks
---

Here is an example sketch by Joshua Davis, from the [hype framework][hype_framework] that has been changed to make use of ruby syntax.
The declared aim of the [hype][hype_library] library is to provide:-
__A collection of classes that performs the heavy lifting so that you can create sketches with the minimum amount of code__. When using the [hype][hype_library] library to create [JRubyArt][jruby_art] sketches it is suggested that you not simply ape the [hype framework][hype_framework] examples, but tailor your sketches to take advantage of the ruby language to create even more elegant code (Code as Art), not that it is relevant here. 

Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.1.1 ide. Note that we can use snake case in place of camel case, for constants use `::` and not `.` to call. The important thing to learn from this sketch is how to implement the `HCallback` interface. This can be implemented as a closure (block), note we do not/should not try and use the vanilla processing method. 

In this case we use the `web_to_color_array` convenience method (since JRubyArt-1.0.7) to convert the web string array to color int in the `HColorPool` constructor.  See how we can easily deal with nested callbacks in JRubyArt, much simpler than creating all those instances of `HCallback` (that could probably all be replaced by java8 lambdas anyway).

### tween_example.rb ###

{% highlight ruby %}
# encoding: utf-8
load_library :hype
include_package 'hype'
# namespace for imported classes
module Hype
  java_import 'hype.extended.behavior.HRandomTrigger'
  java_import 'hype.extended.behavior.HTimer'
  java_import 'hype.extended.behavior.HTween'
  java_import 'hype.extended.colorist.HColorPool'
end

PALETTE = %w(#FFFFFF #F7F7F7 #ECECEC #333333 #0095a8 #00616f #FF3300 #FF6600).freeze

def settings
  size(640, 640)
end

def setup
  sketch_title('Tween Example')
  H.init(self)
  H.background(color('#000000'))
  colors = Hype::HColorPool.new(web_to_color_array(PALETTE))
  canvas = HCanvas.new
  H.add(canvas).autoClear(false).fade(1)
  tween_trigger = Hype::HRandomTrigger.new(1.0 / 6)
  tween_trigger.callback do
    r = canvas.add(HRect.new(25 + (rand(0..5) * 25)).rounding(10))
              .stroke_weight(1)
              .stroke(colors.getColor)
              .fill(color('#000000'), 25)
              .loc(rand(0..width), rand(0..height))
              .anchor_at(H::CENTER)
    tween1 = Hype::HTween.new
                         .target(r).property(H::SCALE)
                         .start(0).end(1).ease(0.03).spring(0.95)
    tween2 = Hype::HTween.new
                         .target(r).property(H::ROTATION)
                         .start(-90).end(90).ease(0.01).spring(0.7)
    tween3 = Hype::HTween.new
                         .target(r).property(H::ALPHA)
                         .start(0).end(255).ease(0.1).spring(0.95)
    r.scale(0).rotation(-90).alpha(0)
    timer = Hype::HTimer.new.interval(250).unregister
    tween3.callback { timer.register }
    timer.callback do
      timer.unregister
      tween1.start(1).end(2).ease(0.01).spring(0.99).register
      tween2.start(90).end(-90).ease(0.01).spring(0.7).register
      tween3.start(255).end(0).ease(0.01).spring(0.95).register.callback { canvas.remove(r) }
    end
  end
end

def draw
  H.draw_stage
end

{% endhighlight %}


<img src="/assets/hype_tween.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/
