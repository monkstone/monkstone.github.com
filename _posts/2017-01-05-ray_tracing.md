---
layout: post
title: "Ray Tracing for JRubyArt and Propane"
date: 2017-01-05 06:00:00
categories: jruby_art update
keywords: jruby_art, propane, processing, sunflow
---
Sunflow is an open source global illumination rendering system written in Java written by [Christopher Kulla][fpsunflower]. I have recently updated to [build with maven][maven] using jdk-8 and to use the latest [janino jars][janino]. The aim is to create a gem to make sunflow rendering easily available to JRubyArt and propane users using Joon Hylub Lees [joons-renderer][joons] as a starting point (this very much WIP see [github][github]), and is showing much promise:-

### test.rb (JRubyArt Sketch)
```ruby
load_library :joonsrenderer # here we are still loading as a java library, gem to follow
include_package 'joons'

attr_reader :jr, :eye, :center, :up

def setup
  sketch_title 'Test'
  @jr = JoonsRenderer.new(self)
  # Camera Setting.
  @eye = Vec3D.new(0, 0, 120)
  @center = Vec3D.new(0, 0, -1)
  @up = Vec3D.new(0, 1, 0)
end

def draw
  jr.begin_record # Make sure to include things you want rendered.
  kamera(eye: eye, center: center, up: up)
  perspektiv(fov: PI / 4.0, aspect_ratio: 4 / 3.0, near_z: 5, far_z: 10_000)
  jr.background('cornell_box', 100, 100, 100) # Cornell Box: width, height, depth.
  jr.background('gi_instant') # Global illumination.
  translate(0, 10, -10)
  rotate_y(-PI / 8)
  rotate_x(-PI / 8)
  jr.fill('diffuse', 255, 255, 255)
  box(20)
  jr.end_record # Make sure to end record.
  jr.display_rendered(true) # Display rendered image if render is completed, and the argument is true.
end

def key_pressed
  jr.render if (key == 'r' || key == 'R')  # Press 'r' key to start rendering.
end

def settings
  size(800, 600, P3D)
end

```

### The Processing Sketch

<img src="/assets/captured.png" />

### The RayTraced Image

<img src="/assets/rendered.png" />


 [fpsunflower]:http://sunflow.sourceforge.net/
 [maven]:https://github.com/monkstone/sunflow
 [joons]:https://github.com/joonhyublee/joons-renderer
 [janino]:http://janino-compiler.github.io/janino/
 [github]:https://github.com/monkstone/joonsrenderer
