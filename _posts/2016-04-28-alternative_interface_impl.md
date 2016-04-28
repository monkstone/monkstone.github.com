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



[jruby_art]:https://ruby-processing.github.io/index.html
