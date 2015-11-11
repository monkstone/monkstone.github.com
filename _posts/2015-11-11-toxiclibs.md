---
layout: post
title:  "Toxiclibs libraries for JRubyArt"
date:   2015-11-11 00:00:00
categories: jruby_art update
permalink:   _posts/toxiclibs
---

The recently released [toxicilibs-0.5.0.gem][gem] is an exemplar for how to create and use custom library gems in JRubyArt. The [toxiclibs gem][gem] provides a ruby wrapper for the [toxiclibs libraries][libraries], that make it easy to use toxicilbs libraries in JRubyArt. The gem build now uses maven to download and install jbox2d. It features such usage refinements as stepping through the the physics (in a hidden pre() loop) so you don't have to. See simple liquidy sketch example below (more examples are included in the [gem][gem]):-

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

[gem]:https://github.com/ruby-processing/toxicgem
[libraries]:http://toxiclibs.org/