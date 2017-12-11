---
layout: post
title: "HEC_Johnson sketch"
date: 2017-12-11 07:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, hemesh
---
Here we translate a [hemesh][hemesh] processing sketch to [JRubyArt][jruby_art].
### HEC Johnson Sketch (hemesh example)

```ruby
load_library :hemesh
include_package 'wblut.hemesh'
java_import 'wblut.geom.WB_AABB'
java_import 'wblut.processing.WB_Render'

attr_reader :jmesh, :render, :creator, :box, :current_type

def settings
  size(1000, 1000, P3D)
  smooth(8)
end

def setup
  sketch_title 'HEC_Johnson'
  @box = WB_AABB.new(-250, -250, -250, 250, 250, 250)
  @current_type = 1
  @jmesh = initialize_johnston(current_type)
  @render = WB_Render.new(self)
end

def draw
  background(55)
  setup_lights
  translate(width / 2, height / 2, 0)
  fill(255)
  no_stroke
  draw_text
  rotate_y(map1d(mouse_x, 0..width, -HALF_PI..HALF_PI))
  rotate_x(map1d(mouse_y, 0..height, HALF_PI..-HALF_PI))
  render_mesh(jmesh)
end

def draw_text
  text_align(CENTER)
  text_size(16)
  curr_format = 'Current Johnston solid %s (%d)'
  text(format(curr_format, creator.get_name, current_type), 0, 350)
  text('Left-click for next.', 0, 370)
end

def render_mesh(mesh)
  no_stroke
  render.draw_faces(mesh)
  no_fill
  stroke_weight(1)
  stroke(0)
  render.draw_edges(mesh)
end

def setup_lights
  directional_light(255, 255, 255, 1, 1, -1)
  directional_light(127, 127, 127, -1, -1, 1)
end

def mouse_pressed
  @current_type = current_type < 92 ? current_type.succ : 1
  @jmesh = initialize_johnston(current_type)
end

def initialize_johnston(index)
  @creator = HEC_Johnson.new
  creator.set_edge(100) # edge length of the polyhedron
  creator.set_type(index) # type of archimedean solid, 1 to 92
  mesh = HE_Mesh.new(creator)
  mesh.fitInAABBConstrained(box)
  mesh
end

```

### Saved image

<img src="/assets/hec_johnson.png" />

The [hemesh][hemesh] library is developed by [Frederik Vanhoutte][wblut]

[wblut]:http://www.wblut.com/
[hemesh]:https://github.com/wblut/HE_Mesh
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
