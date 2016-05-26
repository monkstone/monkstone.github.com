---
layout: post
title: "Using HAttractor from the hype Library"
date: 2016-05-26 17:12:00
categories: jruby_art update
keywords: library, hype, JRubyArt, java8

---

Here is an example sketch by Joshua Davis, from the [hype framework][hype_framework] that has been changed to make use of ruby syntax.
The declared aim of the [hype][hype_library] library is to provide:-
__A collection of classes that performs the heavy lifting so that you can create sketches with the minimum amount of code__. When using the [hype][hype_library] library to create [JRubyArt][jruby_art] sketches it is suggested that you not simply ape the [hype framework][hype_framework] examples, but tailor your sketches to take advantage of the ruby language to create even more elegant code (Code as Art), not that it is relevant here. 

Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.0.2 ide. Note that we can use snake case in place of camel case, for constants use `::` and not `.` to call. The important thing to learn from this sketch is how to implement the `HCallback` interface. This can be implemented as a closure (block), note we do not/should not try and use the vanilla processing method. 


### hype_attractor.rb ###

{% highlight ruby %}

# encoding: utf-8
load_library :hype
include_package 'hype'
java_import 'hype.extended.layout.HGridLayout'
java_import 'hype.extended.behavior.HAttractor'

attr_reader :a, :r, :hf

def settings
  size(640, 640, P3D)
end

def setup
  sketch_title 'Attractor'
  H.init(self)
  H.background(color('#242424'))
  H.use3D(true)
  @r = 0
  @a = 0
  ha = HAttractor.new(320, 320, 100, 260).debug_mode(true)
  @hf = ha.get_force(0)
  pool = HDrawablePool.new(576)
  pool.auto_add_to_stage
      .add(HBox.new.depth(10).width(10).height(10))
      .layout(
        HGridLayout.new
                   .start_x(26)
                   .start_y(26)
                   .start_z(0)
                   .spacing(26, 26, 26)
                   .cols(24)
                   .rows(24)
      )
      .on_create do |obj|
        obj.no_stroke
           .fill(color('#ECECEC'))
           .anchor_at(H::CENTER)
        ha.add_target(obj, 20, 0.5)
      end
      .request_all
end

def draw
  lights
  x = 320 + cos(a.radians) * 200
  y = 320 + sin(a.radians) * 200
  @a += 1.0 / 1.4
  hf.loc(x, y, 100)
  translate(width / 2, height / 2)
  rotate_y(r.radians)
  translate(-width / 2, -height / 2)
  @r += 0.4
  H.draw_stage
end
{% endhighlight %}

<img src="/assets/h_attractor.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/