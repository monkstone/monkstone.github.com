---
layout: post
title: "Using HSprite from the hype Library"
date: 2016-05-25 09:40:00
categories: jruby_art update
keywords: library, hype, JRubyArt, java8, proc

---

Here is an example sketch by Joshua Davis, from the [hype framework][hype_framework] that has been changed to make use of ruby syntax.
The declared aim of the [hype][hype_library] library is to provide:-
__A collection of classes that performs the heavy lifting so that you can create sketches with the minimum amount of code__. When using the [hype][hype_library] library to create [JRubyArt][jruby_art] sketches it is suggested that you not simply ape the [hype framework][hype_framework] examples, but tailor your sketches to take advantage of the ruby language to create even more elegant code (Code as Art), not that it is relevant here. 

Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.2.1 ide. Note that we can use snake case in place of camel case, for constants use `::` and not `.` to call. The important thing to learn from this sketch is how to implement the `HCallback` interface. This can be implemented as a closure (block), note we do not/should not try and use the vanilla processing method. 

In this case we use the `web_to_color_array` convenience method (since JRubyArt-1.0.7) to convert the web string array to color int in the `HColorPool` constructor.  Note that we do not create a copy of the `obj` from the callback, as this appears to be an uneccesary step.

### hype_sprite.rb ###

```ruby
#encoding: utf-8
load_library :hype
include_package 'hype'
java_import 'hype.extended.behavior.HOrbiter3D'
java_import 'hype.extended.behavior.HOscillator'
java_import 'hype.extended.colorist.HColorPool'

PALETTE = %w(#FFFFFF #F7F7F7 #ECECEC #CCCCCC #999999 #666666 #4D4D4D #333333 #FF3300 #FF6600).freeze
attr_reader :pool

def settings
  size(640, 640, P3D)
end

def setup
  sketch_title 'Sprite'
  H.init(self)
  H.background(color('#242424'))
  H.use3D(true)  
  @pool = HDrawablePool.new(400)
  pool.auto_add_to_stage
      .add(HSprite.new(50,50).texture(HImage.new(data_path('tex1.png'))))
      .add(HSprite.new(50,50).texture(HImage.new(data_path('tex2.png'))))
      .add(HSprite.new(50,50).texture(HImage.new(data_path('tex3.png'))))
      .colorist(HColorPool.new(web_to_color_array(PALETTE)).fill_only)
      .on_create do |obj|
    i = pool.current_index
    obj.anchorAt(H::CENTER).rotation(45)    
    orb = HOrbiter3D.new(width / 2, height / 2, 0)
                    .target(obj)
                    .radius(225)
                    .ySpeed(1)
                    .zSpeed(1)
                    .yAngle((i + 1) *2)
                    .zAngle((i + 1) *3)    
    HOscillator.new
               .target(obj)
               .property(H::SCALE)
               .range(0.1, 1.0)
               .speed(0.1)
               .freq(10)
    
    
    HOscillator.new
               .target(obj)
               .property(H::ROTATION)
               .range(-180, 180)
               .speed(0.1)
               .freq(10)
               .current_step(i)
  end.request_all  
end

def draw
  H.draw_stage
  sketch_title(format('HSprite %d fps', frame_rate))
end
```


<img src="/assets/h_sprite.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/
