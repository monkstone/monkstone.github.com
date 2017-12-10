---
layout: post
title: "HEC_Contour sketch from Images"
date: 2017-12-10 07:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, hemesh
---
Here we translate a [hemesh][hemesh] processing sketch to [JRubyArt][jruby_art].
### HEC Isosurface Image Sketch (hemesh example)

```ruby
load_library :hemesh
include_package 'wblut.hemesh'
java_import 'wblut.processing.WB_Render'

attr_reader :mesh, :render

def settings
  size(1000, 1000, P3D)
  smooth(8)
end

def setup
  sketch_title 'HEC_Isosurface_Image example'
  ArcBall.init(self)
  format_string = '/square-%03d.png' # format up to 3 leading zeroes
  images = (1..360).map { |index| data_path(format(format_string, index)) }
  creator = HEC_IsoSurface.new
  creator.set_size(8, 8, 8)
  creator.set_values(images.to_java(:string), self, 64, 64, 64)
  creator.set_isolevel(128)
  creator.set_invert(false)
  creator.set_boundary(-20_000)
  creator.set_gamma(0.3)
  @mesh = HE_Mesh.new(creator)
  @render = WB_Render.new(self)
end

def draw
  background(55)
  lights
  no_stroke
  render.draw_faces(mesh)
  stroke(0)
  render.draw_edges(mesh)
end

def key_pressed
  return unless key == 's'
  save_frame(data_path('example.png'))
end

```

### Saved image

<img src="/assets/from_image.png" />

The [hemesh][hemesh] library is developed by [Frederik Vanhoutte][wblut]

[wblut]:http://www.wblut.com/
[hemesh]:https://github.com/wblut/HE_Mesh
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
[P5]:https://github.com/sojamo/controlp5
