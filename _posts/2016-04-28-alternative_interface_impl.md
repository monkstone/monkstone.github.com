---
layout: post
title: "Another way to implement a java interface with jruby"
date: 2016-04-28 06:19:00
categories: jruby_art update
keywords: java, interface, impl, comparator
---

As with perl (not python) there is usually more than on way to do it with jruby.  Here I update a recipe from the JRuby Cookbook. Where we implement the `Comparator` interface for an `ArrayList` (purely as an example, it does not make sense in practice).

### comparator_implementation.rb ###

{% highlight ruby %}
# encoding: utf-8
# frozen_string_literal: true
# Update to version in JRuby Cookbook
# By Justin Edelson, Henry Liu
require 'java'

v = java.util.ArrayList.new
v.add 'Lions'
v.add 'Tigers'
v.add 'Bears'

java.util.Collections::sort(
  v, java.util.Comparator.impl do |method, *args|
    case method
    when :compare
      args[0] <=> args[1]
    when :equals
      args[0] == args[1]
    end
  end
)

v.each { |animal| puts animal }
{% endhighlight %}

Output
{% highlight bash %}
Bears
Lions
Tigers
{% endhighlight %}

## JRubyArt PBox2D example ##

Alternative [JRubyArt][jruby_art] (anonymous class) Contact Listener implementation see [standalone class][] version.

### contact_listener_implementation.rb ###

{% highlight ruby %}
# encoding: utf-8
# frozen_string_literal: true
require 'pbox2d'
require 'forwardable'
require_relative 'lib/particle'
require_relative 'lib/boundary'

attr_reader :box2d, :particles, :wall

def settings
  size 400, 400
end

def setup
  sketch_title 'Interface.impl Example'
  @box2d = WorldBuilder.build(app: self)
  box2d.add_listener(
    org.jbox2d.callbacks.ContactListener.impl do |method, *args|
      case method
      when :beginContact
        # Get both fixtures
        f1 = args[0].getFixtureA
        f2 = args[0].getFixtureB
        # Get both bodies
        b1 = f1.getBody
        b2 = f2.getBody
        # Get our objects that reference these bodies
        o1 = b1.getUserData
        o2 = b2.getUserData
        if [o1, o2].all? { |obj| obj.instance_of? Particle }
          o1.change
          o2.change
        end
      when :endContact
      when :preSolve
      when :postSolve
      end
    end
  )
  @particles = []
  @wall = Boundary.new(self, width / 2, height - 5, width, 10)
end

def draw
  col = color('#ffffff')
  background(col)
  particles << Particle.new(self, rand(width), 20, rand(4..8)) if rand < 0.1
  particles.each(&:display)
  particles.reject!(&:done)
  wall.display
end
{% endhighlight %}

[standalone class]:https://github.com/ruby-processing/jbox2d/blob/master/examples/test_contact/lib/custom_listener.rb
[jruby_art]:https://ruby-processing.github.io/index.html
