---
layout: post
title: "Using HPolarLayout and HOscillator from the hype Library"
date: 2016-05-25 17:12:00
categories: jruby_art update
keywords: library, hype, JRubyArt, java8

---

Here is an example sketch by Joshua Davis, from the [hype framework][hype_framework] that has been changed to make use of ruby syntax.
The declared aim of the [hype][hype_library] library is to provide:-
__A collection of classes that performs the heavy lifting so that you can create sketches with the minimum amount of code__. When using the [hype][hype_library] library to create [JRubyArt][jruby_art] sketches it is suggested that you not simply ape the [hype framework][hype_framework] examples, but tailor your sketches to take advantage of the ruby language to create even more elegant code (Code as Art), not that it is relevant here. 

Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.1.1 ide. Note that we can use snake case in place of camel case, for constants use `::` and not `.` to call. The important thing to learn from this sketch is how to implement the `HCallback` interface. This can be implemented as a closure (block), note we do not/should not try and use the vanilla processing method. 

Note that hype constants require the `H::` prefix. 

### hype_polar_layout.rb ###

{% highlight ruby %}

# encoding: utf-8
load_library :hype
include_package 'hype'
java_import 'hype.extended.layout.HPolarLayout'
java_import 'hype.extended.behavior.HOscillator'
java_import 'hype.extended.colorist.HColorPool'

PALETTE = %w(#FFFFFF #F7F7F7 #ECECEC #CCCCCC #999999 #666666 #4D4D4D #333333 #242424 #202020 #111111 #080808 #000000).freeze
attr_reader :pool, :colors, :box_depth

def settings
  size(640, 640, P3D)
end

def setup
  sketch_title 'PolarLayout'
  H.init(self)
  H.background(color('#000000'))
  H.use3D(true)
  @ranSeedNum = 1
  @box_depth = 60
  @colors = HColorPool.new(web_to_color_array(PALETTE))
  layout = HPolarLayout.new(0.7, 0.1).offset(width / 2, height / 2)
  @pool = HDrawablePool.new(600)
  pool.auto_add_to_stage
    .add(HBox.new.depth(box_depth).width(box_depth).height(box_depth))
    .layout(layout)
    .on_create do |obj|
      i = pool.current_index
      HOscillator.new.target(obj).property(H::Z).range(-900, 100).speed(1).freq(1).current_step(i)
      HOscillator.new.target(obj).property(H::ROTATION).range(-360, 360).speed(0.05).freq(3).current_step(i)
      HOscillator.new.target(obj).property(H::ROTATIONX).range(-360, 360).speed(0.3).freq(1).current_step(i * 2)
      HOscillator.new.target(obj).property(H::ROTATIONY).range(-360, 360).speed(0.3).freq(1).current_step(i * 2)
      HOscillator.new.target(obj).property(H::ROTATIONZ).range(-360, 360).speed(0.5).freq(1).current_step(i * 2)
    end
    .request_all
end

def draw
  lights
  i = 0
  pool.each do |d|
    d.no_stroke.fill(colors.get_color(i * @ranSeedNum))
    i += 1
  end
  @ranSeedNum += 0.5
  @ranSeedNum = 1 if @ranSeedNum > 300
  H.draw_stage
end
{% endhighlight %}

<img src="/assets/polar_layout.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/