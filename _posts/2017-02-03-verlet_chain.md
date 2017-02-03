---
layout: post
title: "Sophisticated use of a Struct as a Boundary in JRubyArt"
date: 2017-02-02 13:00:00
categories: jruby_art update
keywords: jruby_art, verlet physics, local library, boundary
---
[Ira Greenberg][greenberg] created this processing sketch, but it does not look very pretty in vanilla processing.  Here I have used the ruby language to create a more elegant sketch, that is also somewhat more Object Orientated, without adding too many objects. In particular I have created Boundary Struct that makes it much easier to test boundary conditions for the VerletBall. I have further simplified the interfaces of both VerletBall and VerletStick which result in a much simplified sketch. I have even include an extra link it the chain but performance is fine.

### verlet_ball.rb (in local library)

```ruby
# frozen_string_literal: true
# Chain link end as a ball
class VerletBall
  include Processing::Proxy
  attr_reader :pos, :pos_old, :push, :radius, :x_bound, :y_bound

  def initialize(pos, push, radius)
    @pos = pos
    @push = push
    @radius = radius
    @pos_old = Vec2D.new(pos.x, pos.y)
    # start motion
    @pos += push
    @x_bound = Boundary.new(radius, width - radius)
    @y_bound = Boundary.new(radius, height - radius)
  end

  def run
    verlet
    render
    bounds_collision
  end

  def adjust(vec)
    @pos += vec
  end

  private

  def verlet
    pos_temp = Vec2D.new(pos.x, pos.y)
    @pos += (pos - pos_old)
    @pos_old = pos_temp
  end

  def render
    ellipse(pos.x, pos.y, radius, radius)
  end

  def bounds_collision
    if x_bound.exclude? pos.x
      pos.x = constrain pos.x, x_bound.lower, x_bound.upper
      pos_old.x = pos.x
      pos.x = (pos.x <= radius)? pos.x + push.x : pos.x - push.x
    end
    return unless y_bound.exclude? pos.y
    pos.y = constrain pos.y, y_bound.lower, y_bound.upper
    pos_old.y = pos.y
    pos.y = (pos.y <= radius)? pos.y + push.y : pos.y - push.y
  end
end

# We can easily create and test bounds in ruby
Boundary = Struct.new(:lower, :upper) do
  def exclude? val
    true unless (lower..upper).cover? val
  end
end
```

### verlet_stick.rb (in local library)

```ruby
# frozen_string_literal: true
# Chain link end as a ball
class VerletStick
  include Processing::Proxy
  attr_reader :ball_1, :ball_2, :stiffness, :len

  def initialize(ball_1, ball_2, stiffness)
    @ball_1 = ball_1
    @ball_2 = ball_2
    @stiffness = stiffness
    @len = ball_1.pos.dist(ball_2.pos)
  end

  def run
    render
    constrain_len
  end

  private

  def render
    begin_shape
    vertex(ball_1.pos.x, ball_1.pos.y)
    vertex(ball_2.pos.x, ball_2.pos.y)
    end_shape
  end

  def constrain_len
    delta = ball_2.pos - ball_1.pos
    delta_length = delta.mag
    difference = ((delta_length - len) / delta_length)
    ball_1.adjust delta * (0.5 * stiffness * difference)
    ball_2.adjust delta * (-0.5 * stiffness * difference)
  end
end
```

### The JRubyArt sketch

```ruby

#
# Verlet Integration - ragdoll chain
# after a sketch by Ira Greenberg
#
load_library :verlet_chain
PARTICLES = 5
LINKS = PARTICLES - 1

attr_reader :sticks, :balls

def settings
  size(400, 400)
end

def setup
  sketch_title 'Verlet Chain'
  ellipse_mode(RADIUS)
  rshape = 40
  tension = 0.05
  @sticks = []
  @balls = []
  (0..PARTICLES).each do |i|
    push = Vec2D.new(rand(3..6.5), rand(3..6.5))
    pos = Vec2D.new(width / 2 + (rshape * i), height / 2)
    balls << VerletBall.new(pos, push, 5.0)
    next if i.zero?
    sticks << VerletStick.new(balls[i - 1], balls[i], tension)
  end
end

def draw
  background(255)
  sticks.each(&:run)
  balls.each(&:run)
end
```

To create the local library I created a verlet_chain folder (nested in a library folder) and I created simple `verlet_chain.rb` file that points to the classes.


### verlet_chain.rb

```ruby
frozen_string_literal: true
require_relative 'lib/verlet_ball'
require_relative 'lib/verlet_stick'
```

<video src="{{site.github.url}}/assets/verlet_chain.ogv" poster="{{site.github.url}}/assets/Averlet_chain..png" width="400" height="400" controls preload></video>

[greenberg]:https://github.com/irajgreenberg/workshopExamples/tree/master/verlet_integration_01b_chain
