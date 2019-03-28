---
layout: post
title: "Pure JRubyArt Lenny Explorer after Leander Herzog"
date: 2019-03-24 06:00:00
categories: jruby_art update
permalink: /pure/
keywords: ruby-processing, JRubyArt, processing, Leander Herzog, Explorer
---
### A homage to Leander Herzog 'The Explorer'

The sketch demonstrates the use of polar coordinates for steering and simplified Line2D instances for finding line intersections.
 * The underlying process of the piece is as follows: update steering direction, move forward if no existing line is being crossed, else search for a new direction

 * Featuring JRubyArt Vec2D class

 * Featuring custom Circle and Rect classes

 * Featuring custom Path, Boundary and Line2D classes

#### The Sketch
```ruby
load_library :lenny
attr_reader :path

def settings
  size(600, 600)
end

def setup
  sketch_title 'Lenny Explorer'
  no_fill
  # @path = Path.new(
  #   Boundary.new(
  #     Rect.new(
  #       Vec2D.new(10, 10),
  #       Vec2D.new(width - 20, height - 20)
  #     )
  #   ),
  #   10,
  #   0.03,
  #   3_000
  # )
  @path = Path.new(
    Boundary.new(Circle.new(Vec2D.new(width / 2, height / 2), 250)),
    10,
    0.03,
    3_000
  )
end

def draw
  background(255)
  50.times { path.grow }
  path.render(g)
end

def mouse_pressed
  save_frame data_path('lenny.png')
end

```

#### Custom Boundary class

```ruby

class Boundary
  attr_reader :bounds
  def initialize(bounds)
    @bounds = bounds
  end

  def contains?(vec)
    bounds.contains?(vec)
  end

  def centroid
    bounds.centroid
  end
end

```

#### Custom Line2D

```ruby
class Line2D
  attr_reader :a, :b
  def initialize(a, b)
    @a = a
    @b = b
  end

  def intersecting?(line)
    denom = (line.b.y - line.a.y) * (b.x - a.x) - (line.b.x - line.a.x) * (b.y - a.y)
    na = (line.b.x - line.a.x) * (a.y - line.a.y) - (line.b.y - line.a.y) * (a.x - line.a.x)
    nb = (b.x - a.x) * (a.y - line.a.y) - (b.y - a.y) * (a.x - line.a.x)
    return false if denom.zero?
    ua = na / denom
    ub = nb / denom
    (ua >= 0.0 && ua <= 1.0 && ub >= 0.0 && ub <= 1.0)    
  end
end
```

#### The path class

```ruby
class Path
  attr_reader :path, :last, :bounds, :cut, :theta, :delta, :speed, :searches

  def initialize(bounds, speed, delta, history)
    @bounds = bounds
    @speed = speed
    @delta = delta
    @theta = 0
    @path = (0..history).map { bounds.centroid.copy }
    @searches = 0
    @last = Vec2D.new(path.first)
  end

  def grow
    @delta = rand(-0.2..0.2) if rand < 0.1
    if !intersecting?
      move
    else
      search
    end
  end

  def move
    @last = path.first
    path.pop
    @theta += delta
    path.unshift last + Vec2D.new(speed * Math.cos(theta), speed * Math.sin(theta))
    @searches = 0
  end

  def search
    @theta += delta
    path[0] = last + Vec2D.new(speed * Math.cos(theta), speed * Math.sin(theta))
    @searches += 1
  end

  def render(gfx)
    gfx.begin_shape
    path.map { |vec| gfx.curve_vertex(vec.x, vec.y) }
    gfx.end_shape
  end

  def intersecting?
    return true unless bounds.contains?(path.first)

    if searches < 100
      a = Line2D.new(path[0], path[1])
      (3...path.length).each do |i|
        b = Line2D.new(path[i], path[i - 1])
        return true if a.intersecting?(b)
      end
    end
    false
  end
end

```

#### Circle class

```ruby
class Circle
  attr_reader :centroid, :radius
  def initialize(center, radius)
    @centroid = center
    @radius = radius
  end

  def contains?(vec)
    centroid.dist(vec) < radius
  end
end
```

#### Rect class

```ruby
class Rect
  attr_reader :vec1, :vec2
  def initialize(vec1, vec2)
    @vec1 = vec1
    @vec2 = vec2
  end

  def centroid
    (vec1 + vec2) / 2
  end

  def contains?(vec)
    xminmax = [vec1.x, vec2.x].minmax
    yminmax = [vec1.y, vec2.y].minmax
    return false if vec.x < xminmax[0]
    return false if vec.x > xminmax[1]
    return false if vec.y < yminmax[0]

    vec.y < yminmax[1]
  end
end
```
<img src="/assets/lenny.png" />
