---
layout: post
title:  "Have fun whilst building your ruby skills"
date:   2015-10-15 06:11:00
categories: jruby_art update
---

Daniel Shiffman has recently published the second edition of his [Learning Processing][shiffman] book updated for processing-3.0. He has also published the [code here][code].  There is an opportunity to flex your ruby skills by porting some more examples. My suggestion is to simply translate the processing code to ruby, as a first pass, as I have done below for Dan Shiffmans Polymorphism sketch, and make sure it works. You can then run `rubocop` or similar to check for the more egregious bits of ruby coding you've done. After that you should look at re-factoring the sketch, because what was good or passable in processing/java can often but be done more elegantly in ruby.

You can send me a tweet @[monkstoneT][twitter]

###Here is a first pass to port the polymorphism sketch

It runs but there is room for improvement

{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism

# One array of Shapes, in ruby we don't need polymorphism to achieve that,
# this is only a 'first draft' of a JRubyArt port. The crucial thing it runs
# further it does demonstrate some useful things about inheritance in ruby. 
require_relative 'circle'
require_relative 'square'

attr_reader :shps

def setup
  sketch_title 'Polymorphism'
  @shps = []
  30.times do
    if rand < 0.5
      shps << Circle.new(320, 180, 32, color(rand(255), 100))
    else
      shps << Square.new(320, 180, 32)
    end
  end
end

def draw
  background(255)
  shps.each(&:jiggle)
  shps.each { |shape| shape.change_color if shape.respond_to? :change_color }
  shps.each(&:display)
end

def settings
  size(480, 270)
end

{% endhighlight %}
Here is the parent Shape class
{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism
# class Shape does not require Processing::Proxy but can pass it on to
# the inheriting classes Square and Circle
class Shape
  include Processing::Proxy
  attr_reader :x, :y, :r

  def initialize(x, y, r, *_args)
    @x = x
    @y = y
    @r = r
  end

  def jiggle
    @x += rand(-1..1.0)
    @y += rand(-1..1.0)
  end

  # A generic shape does not really know how to be displayed.
  # This will be overridden in the child classes.
  def display
    'not implemented'
  end
end
{% endhighlight %}
Here is the Square class
{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism
require_relative 'shape'
# Square class can inherit Processing::Proxy methods from Shape
# Variables are inherited from the parent.
# We could also add variables unique to the Square class if we so desire
class Square < Shape

  def initialize(x, y, r)
    super
  end

  # Inherits jiggle from parent

  # The square overrides its parent for display.
  def display
    rect_mode(CENTER)
    fill(175)
    stroke(0)
    rect(x, y, r, r)
  end
end
{% endhighlight %}
Here is the Circle class
{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism
require_relative 'shape'
# Circle class inherits Processing:Proxy module methods from Shape
# Inherits all instance variables from parent + adding one, This is one area 
# we could improve in ruby by using hash args and a hook method, we might even 
# get away without calling super (anticipating more shapes in the future).
class Circle < Shape  
  attr_reader :c, :x, :y, :r

  def initialize(x, y, r, c)
    super           # Call the parent constructor
    @c = c          # Also deal with this new instance variable
  end

  # Call the parent jiggle, but do some more stuff too
  def jiggle
    super
    # The Circle jiggles both size and location.
    @r += rand(-1..1.0)
    @r = constrain(r, 0, 100)
  end

  # The change_color function is unique to the Circle class.
  def change_color
    @c = color(rand(255))
  end

  def display
    ellipse_mode(CENTER)
    fill(c)
    stroke(0)
    ellipse(x, y, r, r)
  end
end
{% endhighlight %}

###Here is second go at porting the Dan Shiffman sketch

It is definetly getting better.

{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism

# One array of Shapes, in ruby we don't need polymorphism to achieve that,
# this is a 'second draft' of a JRubyArt port. Introducing the hook method,
# keyword args and the post_initialization hook for flexible inheritance.
require_relative 'circle'
require_relative 'square'

attr_reader :shps

def setup
  sketch_title 'Polymorphism'
  @shps = []
  30.times do
    if rand < 0.5
      shps << Circle.new(x: 320, y: 180, r: 32, c: color(rand(255), 100))
    else
      shps << Square.new(x: 320, y: 180, r: 32)
    end
  end
end

def draw
  background(255)
  shps.each(&:jiggle)
  shps.each { |shape| shape.change_color if shape.respond_to? :change_color }
  shps.each(&:display)
end

def settings
  size(480, 270)
end
{% endhighlight %}
The improved Square class, note no call to super is required (wasn't previously either but might have been confusing if left out).
{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism
require_relative 'shape'
# Square class can inherit Processing::Proxy methods from Shape
# Variables are inherited from the parent.
# We could also add variables unique to the Square class if we so desire
class Square < Shape
  # Inherits constructor from parent
  # Inherits jiggle from parent

  # The square overrides its parent for display.
  def display
    rect_mode(CENTER)
    fill(175)
    stroke(0)
    rect(x, y, r, r)
  end
end
{% endhighlight %}
The improved Circle class, now in color, to match improved ruby.
{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism
require_relative 'shape'
# Circle class inherits Processing:Proxy module methods from Shape
# Inherits all instance variables from parent + adding one using the 
# post_initialize hook method
class Circle < Shape  
  COLORS = %w{#ff0000 #ffff00 #3333ff #33cc33}
  attr_reader :c, :x, :y, :r

  def post_initialize(args)
    @c = args[:c] # initialize color using post_initialize hook in Shape
  end

  # Call the parent jiggle, but do some more stuff too
  def jiggle
    super
    # The Circle jiggles both size and location.
    @r += rand(-1..1.0)
    @r = constrain(r, 0, 100)
  end

  # The change_color function is unique to the Circle class.
  def change_color
    @c = color(COLORS.sample)
  end

  def display
    ellipse_mode(CENTER)
    fill(c)
    stroke(0)
    ellipse(x, y, r, r)
  end
end
{% endhighlight %}

###Finally we have a suitably refactored port the Dan Shiffman sketch
Here the inheriting classes use a common interface (thus reducing coupling)
{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism

# One array of Shapes, in ruby we don't need polymorphism to achieve that,
# this is a JRubyArt port. Introducing the hook method,
# keyword args and the post_initialization hook for flexible inheritance.
# Important change we only really need to know 'run' the method initialization 
# of color is now really irrelevant. Retained to illustrate how to use a hook.
require_relative 'circle'
require_relative 'square'

attr_reader :shps

def setup
  sketch_title 'Polymorphism'
  @shps = []
  30.times do
    if rand < 0.5
      shps << Circle.new(x: 320, y: 180, r: 32, c: color(rand(255), 100))
    else
      shps << Square.new(x: 320, y: 180, r: 32)
    end
  end
end

def draw
  background(255)
  shps.each(&:run)
end

def settings
  size(480, 270)
end
{% endhighlight %}
The abstract Shape class
{% highlight ruby %}
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism
# class Shape does not require Processing::Proxy but can pass it on to
# the inheriting classes Square and Circle (NB: change to using run)
class Shape
  include Processing::Proxy
  attr_reader :x, :y, :r

  def initialize(x:, y:, r:, **opts)
    @x = x
    @y = y
    @r = r
    post_initialize(opts)
  end

  def jiggle
    @x += rand(-1..1.0)
    @y += rand(-1..1.0)
  end

  def post_initialize(_args)
    'not implemented'
  end

  # A generic shape does not really know how to be displayed.
  # This will be overridden in the child classes.
  def display
    'not implemented'
  end
  
  def run
    'not implemented'
  end
end
{% endhighlight %}
The Square class
{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# http://www.learningprocessing.com

# Example 22-2: Polymorphism
require_relative 'shape'
# Square class can inherit Processing::Proxy methods from Shape
# Variables are inherited from the parent.
# We could also add variables unique to the Square class if we so desire
# NB: run could be the only visible method
class Square < Shape
  # Inherits constructor from parent
  # Inherits jiggle from parent

  # The square overrides its parent for display.
  def display
    rect_mode(CENTER)
    fill(175)
    stroke(0)
    rect(x, y, r, r)
  end
  
  def run
    jiggle
    display
  end
end
{% endhighlight %}
The Circle class now has transaparent colors
{% highlight ruby %}
# post_initialize hook method. Use of change_color makes initilization of @c
# irrelevant, but without example is spoiled.
class Circle < Shape  
  COLORS = %w{#ff0000 #ffff00 #3333ff #33cc33}
  attr_reader :c, :x, :y, :r

  def post_initialize(args)
    @c = args[:c]          # Also deal with this new instance variable
  end

  # Call the parent jiggle, but do some more stuff too
  def jiggle
    super
    # The Circle jiggles both size and location.
    @r += rand(-1..1.0)
    @r = constrain(r, 0, 100)
  end

  # The change_color function is unique to the Circle class.
  def change_color
    @c = color(COLORS.sample) - (100<<24)
  end

  def display
    ellipse_mode(CENTER)
    fill(c)
    stroke(0)
    ellipse(x, y, r, r)
  end
  
  def run
    jiggle
    change_color
    display
  end
end
{% endhighlight %}
I have also ported [The Nature of Code][nature] to JRubyArt.

[nature]:https://github.com/ruby-processing/The-Nature-of-Code-for-JRubyArt
[shiffman]:http://learningprocessing.com/
[code]:https://github.com/shiffman/LearningProcessing
[twitter]:https://twitter.com/monkstoneT
