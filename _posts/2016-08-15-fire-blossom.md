---
layout: post
title: "An old ruby-processing sketch in propane"
date: 2016-08-15 10:00:00
categories: jruby_art update
keywords: ruby-processing, propane

---
Here is an example of a static sketch in propane (there is no need for a draw loop)

```ruby
# frozen_string_literal: true
# After ruby-processing sketch 
# by Danielle Kefford aka quephird
require 'propane'

BLOSSOM_COLORS = %w(
  #3F0700
  #662309
  #8E1400
  #C54603
  #BD7D01
  #DFDE6A
  #BEEDE5
  #848B3E
).freeze

# sketch
class FireBlossom < Propane::App
  attr_reader :colors
  def settings
    size 1920, 1024
  end

  def setup
    sketch_title 'Fire Blossom'
    @colors = web_to_color_array(BLOSSOM_COLORS)
    background 0
    draw_lines
  end

  def func(r, t)
    r + r * sin(5.0 * t) / 10.0 + r * sin(50.0 * t) / 10.0
  end

  def blossom_ring_segment(r, t, dt)
    f1 = func(r, t)
    x1 = f1 * cos(t)
    y1 = f1 * sin(t)
    f2 = func(r, t + dt)
    x2 = f2 * cos(t + dt)
    y2 = f2 * sin(t + dt)
    line x1, y1, x2, y2
  end

  def blossom_ring(r, c)
    stroke(*c)
    dt = Math::PI / 360
    (0..TWO_PI).step(dt).each { |t| blossom_ring_segment r, t, dt }
  end

  def draw_lines
    translate width / 2, height / 2
    stroke_width 2
    (0..1000).each { |r| blossom_ring r, colors[(r / 30) % colors.length] }
    save_frame data_path('fire_blossom.png')
  end
end

FireBlossom.new
```

### What's it look like ###


![fire-blossom.png]({{ github.site.url }}/assets/fire-blossom.png)


