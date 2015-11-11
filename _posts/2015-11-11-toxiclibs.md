---
layout: post
title:  "Toxiclibs libraries for JRubyArt"
date:   2015-11-11 00:00:00
categories: jruby_art update
permalink:   _posts/toxiclibs
---

The recently released [toxiclibs-0.5.0.gem][gem] is an exemplar for how to create and use custom library gems in JRubyArt. The [toxiclibs gem][gem] provides a ruby wrapper for the [toxiclibs libraries][libraries], that make it easy to use toxiclibs libraries in JRubyArt. The gem build now uses maven to build an updated toxiclibs. Now using jdk8 lambdas with Collections.  Here is simulation sketch:-

{% highlight ruby %}
require 'toxiclibs'

NUM_ITERATIONS = 10
attr_reader :gs, :tone_map

def settings
  size 256, 256, P2D
end

def setup
  sketch_title 'Gray Scott Tone Map'
  @gs = Simulation::GrayScott.new width, height, false
  @gs.set_coefficients 0.021, 0.076, 0.12, 0.06
  # create a color gradient for 256 values
  grad = Toxi::ColorGradient.new
  grad.add_color_at(0, Toxi::NamedColor::BLACK)
  grad.add_color_at(128, Toxi::NamedColor::RED)
  grad.add_color_at(192, Toxi::NamedColor::YELLOW)
  grad.add_color_at(255, Toxi::NamedColor::WHITE)
  # this gradient is used to map simulation values to colors
  # the first 2 parameters define the min/max values of the
  # input range (Gray-Scott produces values in the interval of 0.0 - 0.5)
  # setting the max = 0.33 increases the contrast
  @tone_map = Toxi::ToneMap.new 0, 0.33, grad
end

def draw
  @gs.set_rect(mouse_x, mouse_y, 20, 20) if mouse_pressed?
  load_pixels
  # update the simulation a few time steps
  NUM_ITERATIONS.times { @gs.update(1) }
  # read out the V result array
  # and use tone map to render colours
  gs.v.length.times do |i|
    # NB: don't camel case convert here
    pixels[i] = tone_map.getARGBToneFor(gs.v[i])
  end
  update_pixels
end

def key_pressed
  @gs.reset
end

{% endhighlight %}

<img src="/assets/tonemap.png" />

Here is a Volume Utils sketch, featuring Mesh to PShape and povray export:-

{% highlight ruby %}
require 'toxiclibs'

RES = 64
ISO = 0.2
MAX_ISO = 0.66

attr_reader :mesh, :vbo, :curr_zoom, :implicit

def settings
  size(720, 720, P3D)
end

def setup
  sketch_title 'Implicit Surface'
  Processing::ArcBall.init(self)
  @vbo = Gfx::MeshToVBO.new(self)
  @curr_zoom = 1
  vol = EvaluatingVolume.new(TVec3D.new(400, 400, 400), RES, RES, RES, MAX_ISO)
  surface = Volume::HashIsoSurface.new(vol)
  @mesh = WETriangleMesh.new
  surface.compute_surface_mesh(mesh, ISO)
  @is_wire_frame = false
  no_stroke
  @implicit = vbo.mesh_to_shape(mesh, true)
  implicit.setFill(color(222, 222, 222))
  implicit.setAmbient(color(50, 50, 50))
  implicit.setShininess(color(10, 10, 10))
  implicit.setSpecular(color(50, 50, 50))
end

def draw
  background(0)
  lights
  define_lights
  shape(implicit)
end

def key_pressed
  case key
  when 'l', 'L'
    LaplacianSmooth.new.filter(mesh, 1)
    @implicit = vbo.mesh_to_shape(mesh, true)
    # new mesh so need to set finish
    implicit.setFill(color(222, 222, 222))
    implicit.setAmbient(color(50, 50, 50))
    implicit.setShininess(color(10, 10, 10))
    implicit.setSpecular(color(50, 50, 50))
  when 's', 'S'
    save_frame('implicit.png')
  when 'p', 'P'
    no_loop
    pm = Gfx::POVMesh.new(self)
    file = java.io.File.new('implicit.inc')
    pm.begin_save(file)
    # pm.set_texture(Gfx::Textures::WHITE)
    pm.saveAsPOV(mesh, true)
    pm.end_save
    exit
  end
end

def define_lights
  ambient_light(50, 50, 50)
  point_light(30, 30, 30, 200, -150, 0)
  directional_light(0, 30, 50, 1, 0, 0)
  spot_light(30, 30, 30, 0, 40, 200, 0, -0.5, -0.5, PI / 2, 2)
end

# Custom evaluating Volume Class
class EvaluatingVolume < Volume::VolumetricSpace

  attr_reader :upper_bound
  FREQ = Math::PI * 3.8

  def initialize(scal_vec, resX, resY, resZ, upper_limit)
    super(scal_vec, resX, resY, resZ)
    @upper_bound = upper_limit
  end

  def clear
    # nothing to do here
  end

  def getVoxelAt(i)
    getVoxel(i % resX, (i % sliceRes) / resX, i / sliceRes)
  end

  def getVoxel(x, y, z)  # can't overload so we renamed
    val = 0
    if (x > 0 && x < resX1 && y > 0 && y < resY1 && z > 0 && z < resZ1)
      xx = x * 1.0 / resX - 0.5 # NB: careful about integer division !!!
      yy = y * 1.0 / resY - 0.5
      zz = z * 1.0 / resZ - 0.5
      #val = Math.sin(xx * FREQ) + Math.cos(yy * FREQ) + Math.sin(zz * FREQ)
      val = Math.cos(xx * FREQ) * Math.sin(yy * FREQ) + Math.cos(yy * FREQ) * Math.sin(zz * FREQ) + Math.cos(zz * FREQ) * Math.sin(xx * FREQ)
      return 0 if val > upper_bound
    end
    val
  end
end

{% endhighlight %}

<img src="/assets/implicit.png" />

[gem]:https://github.com/ruby-processing/toxicgem
[libraries]:http://toxiclibs.org/
