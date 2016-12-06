---
layout: post
title: "Sketching Math in JRubyArt"
date: 2016-12-06 09:00:00
categories: jruby_art update
keywords: Vec2D, ruby, propane, circumcircle
---
For JRubyArt and propane you need to have a pretty good reason not use Vec2D and Vec3D as replacements for PVector as my recent experiments with creating a circumcircle for 3 points have confirmed. We can use a Vec2D cross product in a collinearity test (impossible with PVector). Further to determine the midpoint of two vectors is as simple as `(a + b) / 2`, you could this with PVector but it is just ugly, and furthermore you are needlessly carrying a third dimension. Here is my most recent sketch:-

### circumcircle_sketch.rb ###

{% highlight ruby %}

# Loosely based on a sketch by Bárbara Almeida
# https://www.openprocessing.org/sketch/179567
# Here is a sketch that clearly demonstrates some of the most egregious parts of
# vanilla processing:-
# PVector is over complicated and overloads 2D and 3D functionality, cf Vec2D
# JRubyArt and propane which behaves as 2D vector (cross product is a float) and
# can be used in chain calculations.
# The inverted Y axis in processing requires remapping to use in graphs etc.
# The map method of processing can be used to this remapping, however in many
# languages (python, ruby etc) map is a keyword with alternative usage.
# For this reason in JRubyArt and propane we have replaced vanilla processing
# map with map1d.

##################################################
# Usage click on screen with mouse to generate a point, repeat as required
# to create a triangle, and the circumcircle will be drawn unless the points are
# collinear. Additional clicks erase the first point and add a new point. To
# demonstrate collinear test press 'c' or 'C', press space-bar to clear
##################################################

load_library :circle

attr_reader :point, :font, :points, :circle, :center

def settings
  size 800, 800
  # pixel_density(2) # for HiDpi screens
  # smooth # see https://processing.org/reference/smooth_.html
end

def setup
  sketch_title 'Circumcircle Through Three Points'
  @point = MathPoint.new
  @font = create_font('Arial', 16, true)
  @points = TPoints.new
  ellipse_mode RADIUS
end

def draw
  graph_paper
  point.display
  points.map(&:display)
  return unless circle
  center.display
  no_fill
  stroke 200
  triangle(
    points[0].screen_x,
    points[0].screen_y,
    points[1].screen_x,
    points[1].screen_y,
    points[2].screen_x,
    points[2].screen_y
  )
  ellipse(center.screen_x, center.screen_y, circle.radius, circle.radius)
end

def mouse_pressed
  points << MathPoint.new.from_sketch(mouse_x, mouse_y)
  return unless points.full?
  @circle = Circumcircle.new(points.array)
  circle.calculate
  @center = MathPoint.new(circle.center)
end

def key_pressed
  case key
  when ' '
    points.clear
  when 'c', 'C'
    points << MathPoint.new(Vec2D.new(0, 0))
    points << MathPoint.new(Vec2D.new(100, 100))
    points << MathPoint.new(Vec2D.new(300, 300))
    puts 'collinear points' if points.collinear?
  end
end

def graph_paper
  background(0)
  cell_size = 20 # size of the grid
  dim = cell_size * 2
  (dim..width - dim).step(cell_size) do |i|
    stroke(75, 135, 155)
    line(i, dim, i, height - dim)
  end
  (dim..height - dim).step(cell_size) do |i|
    line(dim, i, width - dim, i)
  end
end

{% endhighlight %}

### math_point.rb ###

{% highlight ruby %}

# frozen_string_literal: true
# In processing the Y-axis is inverted, plus we have a fake origin for display
# pos is the position in the Math World, which we need to translate to sketch
class MathPoint
  include Processing::Proxy # to access sketch methods, including height
  OR = 40.0
  FORM = '(%d, %d)'.freeze
  attr_reader :pos, :from_mouse
  def initialize(pos = Vec2D.new)
    @pos = pos
    @from_mouse = false
  end

  def from_sketch(x, y)
    @from_mouse = true
    @pos = Vec2D.new(x - OR, map1d(y, height - OR..-OR, 0..height))
    self
  end

  def xpos
    pos.x
  end

  def ypos
    pos.y
  end

  def screen_x
    pos.x + OR
  end

  def screen_y
    map1d(pos.y, 0..height, height - OR..-OR)
  end

  def display
    fill 200
    no_stroke
    ellipse(screen_x, screen_y, 2.5, 2.5)
    display_text
    return unless from_mouse
    no_fill
    stroke(200, 0, 0)
    ellipse(screen_x, screen_y, 5, 5)
  end

  private

  def display_text
    from_mouse ? fill(255, 255, 0) : fill(0, 255, 0)
    text_font(font, 12)
    text(format(FORM, xpos.round, ypos.round), screen_x + 5, screen_y - 5)
  end
end

{% endhighlight %}

### points.rb ###

Here we create a custom enumerable class, that incorporates our collinear test, try this in java!!!

{% highlight ruby %}

# frozen_string_literal: true
require 'forwardable'
MAX_POINT = 3
# A collection of a maximum of 3 points in a Math World
# includes a collinearity test, and a map to simple Vec2D array
class TPoints
  extend Forwardable
  def_delegators(:@points, :each, :map, :size, :shift, :clear, :[])
  include Enumerable

  attr_reader :points

  def initialize
    @points = []
  end

  def <<(pt)
    points << pt
    shift if size > MAX_POINT
  end

  def collinear?
    return false unless full?
    (array[0] - array[1]).cross(array[1] - array[2]).zero?
  end

  def array
    points.map(&:pos)
  end

  def full?
    points.length == MAX_POINT
  end
end

{% endhighlight %}

### circumcircle.rb ###

Here we isolate the Collinear functionality in a class that has no `processing` / `java` dependencies.

{% highlight ruby %}

# frozen_string_literal: true
PBisector = Struct.new(:vector, :angle) # perpendicular bisector
Vect = Struct.new(:x, :y, :z) # for calculation of center
# Circumcircle from 3 points
class Circumcircle
  include Math
  attr_reader :center, :radius, :points
  def initialize(points)
    @points = points
  end

  def calculate
    ab = bisector(points[0], points[1]) # find 2 midpoints
    bc = bisector(points[1], points[2])
    @center = circumcenter(ab, bc)
    @radius = center.dist(points[2]) # points[2] = c
  end

  def bisector(a, b)
    midpoint = (a + b) / 2.0 # middle of ab (or bc)
    theta = atan2(b.y - a.y, b.x - a.x) # slope of ab (or bc)
    PBisector.new(midpoint, theta - PI / 2)
  end

  def circumcenter(pb1, pb2)
    # equation of the first bisector (ax - y =  -b)
    a0 = tan pb1.angle
    v0 = pb1.vector
    a1 = tan pb2.angle
    v1 = pb2.vector
    eq0 = Vect.new(a0, -1, -1 * (v0.y - v0.x * a0))
    eq1 = Vect.new(a1, -1, -1 * (v1.y - v1.x * a1))
    # calculate x and y coordinates of the circumcenter
    ox = (eq1.y * eq0.z - eq0.y * eq1.z) /
         (eq0.x * eq1.y - eq1.x * eq0.y)
    oy = (eq0.x * eq1.z - eq1.x * eq0.z) /
         (eq0.x * eq1.y - eq1.x * eq0.y)
    Vec2D.new(ox, oy)
  end
end

{% endhighlight %}

<img src="/assets/circumcircle.png" />

See [collinear at wolfram math world](http://mathworld.wolfram.com/Collinear.html)
