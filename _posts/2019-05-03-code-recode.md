---
layout: post
title: "Code-ReCode"
date: 2019-05-03 06:00:00
categories: jruby_art update
keywords: JRubyArt, Leander Herzog, William
---

```ruby
# This sketch is part of the ReCode Project - http://recodeproject.com
# From Computer Graphics and Art vol2 no3 pg 13
# "Organic Illusion"
# by William Kolomyjec
# Leander Herzog 2012
# JRubyArt  Version by Martin Prout 2019
# Creative Commons license CC BY-SA 3.0
attr_reader :williams
ROWS = 2
COLUMNS = 6
UNIT = 200

def settings
  size(UNIT * COLUMNS, UNIT * ROWS)
end

def setup
  sketch_title 'Organic Illusion'
  background(255)
  @williams = []
  grid(COLUMNS, ROWS) do |i, j|
    williams << William.new(Vec2D.new(i * UNIT, j * UNIT))
  end
end

# Seems daft to subclass Vec2D here
class William
  attr_reader :cntr, :points, :offset, :radius

  def initialize(pos)
    @cntr = pos
    @offset = Vec2D.new(rand(-UNIT..UNIT) * 0.2, rand(-UNIT..UNIT) * 0.2)
    res = (UNIT * 0.1 * 2).round
    len = (UNIT * 0.1).round
    @radius = UNIT * 0.25
    angle = 0
    flex = PI
    radial_offset = rand(-flex..flex)
    @points = []
    tmp = Vec2D.new
    dir = Vec2D.new(len, 0)
    res.times do |i|
      angle = (map1d(i, 0..res, 0..TWO_PI) + radial_offset)
      points[i] = Vec2D.new(
        cntr.x + UNIT * 0.5 + sin(angle) * radius + offset.x,
        cntr.y + UNIT * 0.5 + cos(angle) * radius + offset.y
      )
      points[i + res] = cntr + tmp
      tmp += dir
      case(i + 1)
      when res / 4
        dir = Vec2D.new(0, len)
      when res / 2
        dir = Vec2D.new(-len, 0)
      when res / 4 * 3
        dir = Vec2D.new(0, -len)
      end
      line(points[i].x, points[i].y, points[i + res].x, points[i + res].y)
    end
  end
end

```

<img src="/assets/illusion.png" />
