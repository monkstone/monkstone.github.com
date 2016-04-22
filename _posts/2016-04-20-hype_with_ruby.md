---
layout: post
title: "Using ruby with hype Library"
date: 2016-04-20 17:04:00
categories: jruby_art update
keywords: library, hype, JRubyArt
---

Here is an example sketch by Joshua Davis, from the [hype framework][hype_framework] that has been changed to make use of ruby syntax.
The declared aim of the [hype][hype_library] library is to provide:-
__A collection of classes that performs the heavy lifting so that you can create sketches with the minimum amount of code__. When using the [hype][hype_library] library to create [JRubyArt][jruby_art] sketches it is suggested that you not simply ape the [hype framework][hype_framework] examples, but tailor your sketches to take advantage of the ruby language to create even more elegant code (Code as Art). 

Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.0.2 ide. Note that we can use snake case in place of camel case, for constants use `::` and not `.` to call. The important thing to learn from this sketch is how to implement the HCallback interface. This can be implemented as a closure (block), note we do not/should not try and use the vanilla processing method. But we do need to cast the object to a `HDrawable` type we do this using `to_java(Java::Hype::HDrawable)` function.

In this case we use ruby syntax to `dry out` the creation of web colors. We could make the code more literate by creating a hash of web colors (and call the colors by human readable names, although for web artists that might not be necessary?

### color_pool.rb ###

{% highlight ruby %}
# encoding: utf-8
# frozen_string_literal: true
load_library :hype
include_package 'hype'
# Access through Hype namespace
module Hype
  java_import 'hype.extended.colorist.HColorPool'
  java_import 'hype.extended.layout.HGridLayout'
end

WEB_COLORS = %w(#FFFFFF #F7F7F7 #ECECEC #0095A8 #00616F #333333 #FF3300 #FF6600).freeze

def settings
  size(600, 600)
end

def setup
  sketch_title 'Color Pool'
  H.init(self)
  H.background(color('#242424'))
  colors = Hype::HColorPool.new
  WEB_COLORS.each { |col| colors.add(color(col)) }
  pool = HDrawablePool.new(15_876)
  pool.auto_add_to_stage
      .add(HRect.new(5))
      .layout(Hype::HGridLayout.new.start_x(5).start_y(5).spacing(5, 5).cols(126))
      .on_create do |obj|
        i = pool.current_index
        d = obj.to_java(Java::Hype::HDrawable)
        d.no_stroke.fill(colors.get_color(i))
      end
      .request_all
  H.draw_stage
end

{% endhighlight %}

<img src="/assets/color_pool.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/
