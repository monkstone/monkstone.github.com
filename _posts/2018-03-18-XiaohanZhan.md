---
layout: post
title: "Xiaohan Zhang Sketch in JRubyArt"
date: 2018-03-18 06:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, hellochar, chaldini, generative
---
### Xiaohan Zhang has created interesting stuff in processing

Here is a processing sketch by [Xiaohan Zhang aka hellochar][hellochar] roughtly translated for JRubyArt by Martin Prout

```ruby
# After an original by Xiaohan Zhang aka hellochar
load_library :particle
PX_PER_BUCKET = 40
attr_reader :scalar, :g, :t, :particles

def setup
  sketch_title 'March 15'
  @t = 0
  @particles = (0..500).map { |i| Particle.new(i, rand * width, rand * height) }
  background(255)
end

def draw
  noStroke
  fill(12, 6, 19, 155)
  rect(0, 0, width, height)
  stroke_cap(SQUARE)
  5.times do
    dt = mouse_pressed? ? -0.02 : 0.02
    @scalar = 2**map1d(mouse_x, 0..width, -5..5.0) / 32 * 1_000
    @g = map1d(mouse_y, 0..height, 1..10.0)**3
    particles.each(&:compute_force)
    particles.each { |p| p.update(dt) }
    @t += dt
  end
  particles.each(&:draw)
end

def settings
  size(800, 800, P2D)
end

```

The library (library/particle/particle.rb)

```ruby
# The Particle class
class Particle
  include Processing::Proxy
  attr_accessor :fx, :fy
  attr_reader :idx, :x, :y, :vx, :vy

  def initialize(idx, x, y)
    @idx = idx
    @x = x
    @y = y
    @vx = 0
    @vy = 0
    @fx = 0
    @fy = 0
  end

  def compute_force_single(pa)
    g = Processing.app.g
    scalar = Processing.app.scalar
    return if pa == self || idx > pa.idx
    dfx = 0
    dfy = 0
    ox = pa.x - x
    oy = pa.y - y
    dist2 = ox * ox + oy * oy
    # if (dist2 < 5 * 5) return
    dist = Math.sqrt(dist2)
    dist_factor = g * Math.sin(dist / scalar) / dist2
    # dist_factor = min(100, 1 / (dist * dist))
    unless dist_factor.nan?
      dfx += ox * dist_factor
      dfy += oy * dist_factor
    end
    # repel force when very close by
    repel_factor = -1000 / (dist2 * dist)
    unless repel_factor.nan?
      dfx += ox * repel_factor
      dfy += oy * repel_factor
    end
    @fx += dfx
    @fy += dfy
    pa.fx -= dfx
    pa.fy -= dfy
  end

  def compute_force
    Processing.app.particles.each { |p| compute_force_single(p) }
  end

  def update(dt)
    @vx += fx * dt
    @vy += fy * dt
    @vx *= 0.99
    @vy *= 0.99
    px = x
    py = y
    @x += fx * dt
    @y += fy * dt
    out_of_bounds = reset_bounds
    speed = dist(px, py, x, y)
    if !out_of_bounds && speed < 20
      c = lerp_color(color('#2D728F'), color('#F49E4C'), speed / 3)
      stroke(c, 40)
      stroke_weight(6)
      line(px, py, x, y)
      no_stroke
      fill(c)
      # fill(255, 128)
      # rad = Math.sqrt(2 + speed / 3)
      rad = 3
      ellipse(x, y, rad * 2, rad * 2)
    end
    @fx = 0
    @fy = 0
  end

  def reset_bounds
    return false if (0..width).cover?(x) && (0..height).cover?(y)
    @x = rand * width
    @y = rand * height
    @vx = 0
    @vy = 0
    true
  end

  def draw
    ellipse(x, y, 14, 14)
  end
end

```
A snapshot of running sketch

![hellochar.png]({{site.github.url}}/assets/hellochar.png)


[hellochar]:https://github.com/hellochar/
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
