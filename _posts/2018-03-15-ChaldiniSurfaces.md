---
layout: post
title: "Chaldini Surface Sketch in JRubyArt"
date: 2018-03-15 06:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, v3ga, chaldini, paul bourke
---
### Paul Bourke has created fantastic stuff

Here is a processing sketch by [Julien Gachadoat aka v3ga][v3ga] based on [Paul Bourkes][bourke] work translated for JRubyArt by Martin Prout

```ruby
# Chladni plate interference surfaces
# —
# Based on :
# http://paulbourke.net/geometry/chladni/
#
# —
# Developed on:
#   - Processing 2.1.1 on MacOSX (10.9.2)
# Julien Gachadoat aka v3ga
# www.v3ga.net
# www.2roqs.com
#
# Translated and tested on:
#   - JRubyArt-1.4.6
# Martin Prout

attr_reader :m, :n, :epsilon, :recompute

def settings
  size(500, 500, P2D)
end

def setup
  sketch_title 'Chladni'
  @epsilon = 0.05
  @recompute = true
  @m = 10
  @n = 2
end

def draw
  return unless recompute
  @recompute = false
  background(255)
  load_pixels
  grid(width, height) do |x, y|
    chladni = cos(n * PI * x / width) * cos(m * PI * y / width) - cos(m * PI * x / width) * cos(n * PI * y / width)
    pixels[x + y * width] = 0 if chladni.abs <= epsilon
  end
  update_pixels
  format_string = "m= %d n = %d \nepsilon= %0.3f"
  infos = format(format_string, m, n, epsilon)
  fill(255, 0, 0)
  text(infos, 4, 16)
end

def key_pressed
  case key
  when '+'
    @epsilon += 0.01
  when '-'
    @epsilon -= 0.01
  when CODED
    case key_code
    when UP
      @m += 1
    when DOWN
      @m -= 1
    when LEFT
      @n -= 1
    when RIGHT
      @n += 1
    end
  end
  @recompute = true
  @m = constrain(m, 1, 20)
  @n = constrain(n, 1, 20)
end

```

![chladni.png]({{site.github.url}}/assets/chladni.png)

[bourke]:http://paulbourke.net/geometry/chladni/
[v3ga]:http://www.v3ga.net/
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
