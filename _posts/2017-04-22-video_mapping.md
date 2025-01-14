---
layout: post
title: "GLVideo Video Mapping in JRubyArt"
date: 2017-04-22 12:00:00
categories: jruby_art update
keywords: capture, jruby_art, glvideo, processing
---

### GLVideo the future video library for processing

This library uses GStreamer-1.0 (and hence possibility of hardware acceleration).  First you need to make sure you can use the library on vanilla processing, for Archlinux this may need a re-compile
see [my fork](https://github.com/monkstone/processing-glvideo). Here is a more complicated sketch by Gottfreid Haider, translated to JRubyArt see [original here](https://github.com/gohai/processing-glvideo/blob/master/examples/VideoMapping/VideoMapping.pde).

Note how we are able to take advantage of ruby features to make the sketch much more intelligible and more Object Orientated. Nevertheless kudos to Gottfreid Haider for the original sketch and for creating the library.

NB: The glvideo library is still somewhat experimental and the interface may well change.


```ruby
load_libraries :glvideo, :corners
include_package 'gohai.glvideo'

# placeholder for image sources
ImageSource = Struct.new(:video, :image)

attr_reader :sources, :sel, :video, :corners, :quads, :last_mouse_move
RES = 5	# number of subdivisions (e.g. 5 x 5)

def setup
  @last_mouse_move = 0
  no_cursor
  @video = GLMovie.new(self, data_path('launch2.mp4'))
  video.loop
  image = load_image(data_path('checkerboard.png'))
  @sources = ImageSource.new(video, image)
  @sel = sources.video
  @corners = Corners.new(width, height, sources.image.width / 2, sources.image.height / 2)
  @quads = create_mesh(sel, corners, RES)
end

def draw
  background(0)
  video.read if sel.respond_to?(:read) && video.available
  # regenerate mesh if we're dragging a corner
  if corners.selected? && (pmouse_x != mouse_x || pmouse_y != mouse_y)
    corners.set_corner(mouse_x, mouse_y)
    # this improves performance, but will be replaced by a
    # more elegant way in a future release
    @quads = []
    Java::JavaLang::System.gc # this is  generally not recommended
    @quads = create_mesh(sel, corners, RES)
  end

  # display
  quads.map { |quad| shape(quad) } unless quads.empty?
  # hide the mouse cursor after two seconds
  if pmouse_x != mouse_x || pmouse_y != mouse_y
    cursor
    @last_mouse_move = millis
  elsif !last_mouse_move.zero? && 2000 < millis - last_mouse_move
    no_cursor
    @last_mouse_move = 0
  end
end

def mouse_pressed
  corners.each_with_index do |corner, i|
    return corners.set_index(i) if dist(mouse_x, mouse_y, corner.x, corner.y) < 20
  end
  # no corner? then switch texture
  @sel = sel.respond_to?(:loop) ? sources.image : sources.video
  @quads = create_mesh(sel, corners, RES)
end

def mouse_released
  corners.set_index(-1)
end

def create_mesh(tex, corners, res)
  transform = PerspectiveTransform.get_quad_to_quad(
    0, 0, tex.width, 0,                   # top left, top right
    tex.width, tex.height, 0, tex.height, # bottom right, bottom left
    corners[0].x, corners[0].y, corners[1].x, corners[1].y,
    corners[2].x, corners[2].y, corners[3].x, corners[3].y
  )
  warp_perspective = WarpPerspective.new(transform)
  x_step = tex.width.to_f / res
  y_step = tex.height.to_f / res
  quads = []
  (0...res).each do |y|
    (0...res).each do |x|
      texture_mode(NORMAL)
      sh = create_shape
      sh.begin_shape(QUAD)
      sh.no_stroke
      sh.texture(tex)
      sh.normal(0, 0, 1)
      point = warp_perspective.map_dest_point(x * x_step, y * y_step)
      sh.vertex(point.get_x.to_f, point.get_y.to_f, 0, x.to_f / res, y.to_f / res)
      point = warp_perspective.map_dest_point((x + 1) * x_step, y * y_step)
      sh.vertex(point.get_x.to_f, point.get_y.to_f, 0, (x + 1).to_f / res, y.to_f / res)
      point = warp_perspective.map_dest_point((x + 1) * x_step, (y + 1) * y_step)
      sh.vertex(point.get_x.to_f, point.get_y.to_f, 0, (x + 1).to_f / res, (y + 1).to_f / res)
      point = warp_perspective.map_dest_point(x * x_step, (y + 1) * y_step)
      sh.vertex(point.get_x.to_f, point.get_y.to_f, 0, x.to_f / res, (y + 1).to_f / res)
      sh.end_shape
      quads[y * res + x] = sh
    end
  end
  quads
end

def settings
  full_screen(P2D)
end
```

Here is the corners.rb library that just how flexible ruby is

```ruby
# frozen_string_literal: true
require 'forwardable'
# placeholder for vector coordinates
Vect = Struct.new(:x, :y)

# A class to contain frame corners, uses forwardable
class Corners
  include Enumerable
  extend Forwardable
  def_delegators :@corners, :each_with_index, :[], :[]=, :<<
  attr_reader :idx

  def initialize(width, height, ws, wh)
    @corners = [
      Vect.new(width / 2 - ws, height / 2 - wh),
      Vect.new(width / 2 + ws, height / 2 - wh),
      Vect.new(width / 2 + ws, height / 2 + wh),
      Vect.new(width / 2 - ws, height / 2 + wh)
    ]
    @idx = -1
  end

  def set_corner(mx, my)
    self[idx] = Vect.new(mx, my)
  end

  def selected?
    idx != -1
  end

  def set_index(sel)
    @idx = sel
  end
end
```
