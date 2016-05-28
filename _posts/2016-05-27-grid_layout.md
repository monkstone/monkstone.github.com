---
layout: post
title: "Using 3D HGridLayout and HOscillator from the hype Library"
date: 2016-05-27 17:12:00
categories: jruby_art update
keywords: library, hype, JRubyArt, java8

---

Here is an example sketch by Joshua Davis, from the [hype framework][hype_framework] that has been changed to make use of ruby syntax.
The declared aim of the [hype][hype_library] library is to provide:-
__A collection of classes that performs the heavy lifting so that you can create sketches with the minimum amount of code__. When using the [hype][hype_library] library to create [JRubyArt][jruby_art] sketches it is suggested that you not simply ape the [hype framework][hype_framework] examples, but tailor your sketches to take advantage of the ruby language to create even more elegant code (Code as Art), not that it is relevant here. 

Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.1.1 ide.  

### grid_layout.rb ###

{% highlight ruby %}

# encoding: utf-8
load_library :hype
include_package 'hype'
java_import 'hype.extended.layout.HGridLayout'
java_import 'hype.extended.behavior.HOscillator'

attr_reader :pool, :osc, :scale, :r

def settings
  size(640, 640, P3D)
end

def setup
  sketch_title 'Grid Layout'
  H.init(self)
  H.background(color('#242424'))
  H.use3D(true)
  @r = 0
  @scale = 0
  @osc = HOscillator.new.range(0.2, 2.5).speed(10).freq(2)
  @pool = HDrawablePool.new(125)
  pool.auto_add_to_stage
      .add(HBox.new.depth(25).width(25).height(25))
      .layout(
        HGridLayout.new
                   .start_x(180)
                   .start_y(180)
                   .start_z(-140)
                   .spacing(70, 70, 70)
                   .cols(5)
                   .rows(5)
      )
      .request_all
end

def draw
  lights
  translate(width / 2, height / 2)
  rotate_y(r.radians)
  translate(-width / 2, -height / 2)
  @r += 0.3
  i = 0
  pool.each do |d|
    osc.current_step(frame_count + i * 3).next_raw
    @scale = osc.curr
    d.depth(25 * scale).width(25 * scale).height(25 * scale)
    i += 1
  end
  H.draw_stage
end

{% endhighlight %}

<img src="/assets/grid_layout.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/