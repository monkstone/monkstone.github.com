---
layout: post
title: "Using Modules"
date: 2016-02-11 10:20:00
categories: jruby_art update
keywords: runnable, euler, Processing::Proxy, JRubyArt
---

Here is a little processing sketch by Ira Greenberg, that has been refactored and updated for [JRubyArt][jruby_art]. Also demonstrated is how to create and use modules in JRubyArt. We use the runner module to simplify the EulerBall interface, the `Processing::Proxy` module is used to access `PApplet` methods similar to vanilla processings `inner` classes. We create an convenience class to handle boundary collisions in an object orientated way.  Note we can selectively include `Processing::MathTool` to use the `constrain` method (note `map1d` etc are also included in the `MathTool`).

### euler_ball.rb ###

```ruby
# encoding: UTF-8

# module encapsulating run
module Runner
  def run
    move
    render
    bounds_collision
  end
end

# Euler Ball Class, we include Processing::Proxy module to access PApplet methods
class EulerBall
  include Processing::Proxy, Runner
  attr_reader :pos, :spd, :radius, :bounds_x, :bounds_y

  def initialize(position:, speed:, radius:)
    @pos = position
    @spd = speed
    @radius = radius
    @bounds_x = Bounds.new(lower: radius, upper: width - radius)
    @bounds_y = Bounds.new(lower: radius, upper: height - radius)
  end

  def move
    @pos += spd
  end

  def render
    ellipse(pos.x, pos.y, radius * 2, radius * 2)
  end

  def bounds_collision
    pos.x = bounds_x.position pos.x
    spd.x *= -1 unless bounds_x.inside
    pos.y = bounds_y.position pos.y
    spd.y *= -1 unless bounds_y.inside
  end
end

# Convenient boundary class, we only include MathTool module for constrain
class Bounds
  include Processing::MathTool
  attr_reader :low, :high, :inside
  def initialize(lower:, upper:)
    @low = lower
    @high = upper
    @inside = true
  end

  # Returns the current position or the limit, sets the `inside` flag
  def position(val)
    @inside = (low..high).cover? val
    constrain(val, low, high)
  end
end
```

Here is the much simplified `euler_integration` sketch, note the use of the more literate keyword arguments (since ruby 2.0).

### euler_integration.rb ###

```ruby
#
# Euler Integration (v01)
# pos  +=  spd
# sketch after Ira Greenwood 
# Features first class keyword arguments, and use of modules
#
require_relative 'euler_ball'

attr_reader :ball

def setup
  sketch_title 'Euler Integration'
  @ball = EulerBall.new(
    position: Vec2D.new(width / 2, height / 2),
    # create a random direction vector, scaled by 3
    speed: Vec2D.random * 3,
    radius: 10
  )
end

def draw
  background(255)
  # fill(255, 2)
  # rect(-1, -1, width + 1, height + 1)
  fill(0)
  ball.run
end

def settings
  size(400, 400)
  smooth 4
end
```

[jruby_art]:https://ruby-processing.github.io/JRubyArt/
