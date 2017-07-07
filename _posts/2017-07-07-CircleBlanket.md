---
layout: post
title: "Circle Blanket Sketch in JRubyArt"
date: 2017-07-07 12:00:00
categories: jruby_art update
keywords: JRubyArt, grid, step
---

### There's usuall more than one way to do it in ruby

Here are two JRubyArt versions of an [openprocessing sketch by Michael Pinn][sketch]

The first is a more less direct translation from P5*js to JRubyArt, where we use `range` `step` instead of a custom `for` loop.

```ruby
# After an openprocessing sketch
# by Michael Pinn
# translated to JRubyArt by Martin Prout
def settings
  size(640, 640)
end

def setup
  sketch_title 'Circle Blanket'
  no_stroke
end

def draw
  background(0)
  translate(width / 2, height / 2)
  (-100..100).step(10) do |x|
    (-100..100).step(10) do |y|
      d = dist(x, y, 0, 0)
      d2 = sin(d.radians) * d
      fill(0, 150, 255)
      push_matrix
      translate(x, y)
      rotate (d + frame_count).radians
      ellipse(x, y, 5, 5)
      pop_matrix
    end
  end
end
```

In this second version we make use of the grid method (first introduced in ruby-processing) and now incorporated into JRubyArt and propane as a JRuby extension. This powerful method reduces a nested loop into a single loop that accepts a block.

```ruby
# After an openprocessing sketch
# by Michael Pinn
# translated to JRubyArt by Martin Prout
# makes use of :grid method

def settings
  size(640, 640)
end

def setup
  sketch_title 'Grid Circle Blanket'
  no_stroke
end

def draw
  background(0)
  translate(width / 2, height / 2)
  grid(200, 200, 10, 10) do |a, b|
    x = a - 100
    y = b - 100
    d = dist(a, b, 100, 100)
    d2 = sin(d.radians) * d
    fill(0, 150, 255)
    push_matrix
    translate(x, y)
    rotate (d + frame_count).radians
    ellipse(x, y, 5, 5)
    pop_matrix
  end
end
```

[sketch]:https://www.openprocessing.org/sketch/157801
