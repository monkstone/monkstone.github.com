---
layout: post
title: "Lenny Explorer after Leander Herzog"
date: 2019-03-24 06:00:00
categories: jruby_art update
permalink: /LennyExplorer/
keywords: ruby-processing, JRubyArt, processing, toxi, Leander Herzog, Explorer
---
### A homage to Leander Herzog 'The Explorer'
#### After a version by Karsten Schmidt (aka toxi)

The sketch demonstrates the use of polar coordinates for steering and simplified Line2D instances for finding line intersections.
 * The underlying process of the piece is as follows: update steering direction, move forward if no existing line is being crossed, else search for a new direction

 * Featuring toxiclibs Vec2D classes (renamed TVec2D in the gem version)

 * Featuring toxiclibs Circle and Rect classes

 * Featuring JRubyArt Vec2D classes

 * Featuring custom Path, Boundary and Line2D classes

#### The Sketch
```ruby
# lenny_explorer.rb
require 'toxiclibs'
java_import 'toxi.geom.Circle'
java_import 'toxi.geom.Rect'
load_library :lenny

attr_reader :path

def settings
  size(600, 600)
end

def setup
  sketch_title 'Lenny Explorer'
  no_fill
  @path = Path.new(
    RectBoundary.new(Rect.new(10, 10, width - 20, height - 20)),
    10,
    0.03,
    3_000
  )
  # @path = Path.new(
  #   CircularBoundary.new(Circle.new(Vec2D.new(width / 2, height / 2), 250)),
  #   10,
  #   0.03,
  #   3_000
  # )
end

def draw
  background(255)
  50.times { path.grow }
  path.render(g)
end

```

#### Custom Boundary classes

```ruby

# Duck typing Boundary
class CircularBoundary
  attr_reader :circle

  def initialize(circle)
    @circle = circle
  end

  def contains_point(vec)
    circle.contains_point(vec)
  end

  def centroid
    circle # Circle can DuckType as Vec2D
  end
end

# Duck typing Boundary
class RectBoundary
  attr_reader :rectangle

  def initialize(rectangle)
    @rectangle = rectangle
  end

  def contains_point(vec)
    rectangle.contains_point(vec)
  end

  def centroid
    rectangle.get_centroid
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
# Stores and manipulates the path
class Path
  attr_reader :path, :last, :bounds, :cut, :theta, :delta, :speed, :searches

  def initialize(bounds, speed, delta, history)
    @bounds = bounds
    @speed = speed
    @delta = delta
    @theta = 0
    @path = (0..history).map { bounds.centroid.copy }
    @searches = 0
    @last = TVec2D.new(path.first)
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
    path.unshift last.add(TVec2D.new(speed, theta).cartesian)
    @searches = 0
  end

  def search
    @theta += delta
    path[0] = last.add(TVec2D.new(speed, theta).cartesian)
    @searches += 1
  end

  def render(gfx)
    gfx.begin_shape
    path.map { |vec| gfx.curve_vertex(vec.x, vec.y) }
    gfx.end_shape
  end

  def intersecting?
    return true unless bounds.contains_point(path.first)

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
<img src="/assets/lenny.png" />
