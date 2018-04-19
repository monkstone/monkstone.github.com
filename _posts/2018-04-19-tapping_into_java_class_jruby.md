---
layout: post
title: "tapping Into Java with JRuby"
date: 2018-04-19 06:00:00
categories: jruby_art update
permalink: /tapping_into_java/
keywords: tap, jruby, jruby_art, java
---
### Tapping into processing PVector class  ###

I expect most rubyist know how to use `tap` to do some complex initialization in regular ruby, but probably fewer know that with JRuby you can also `tap` into a java class. PVector is class from vanilla processing (that I would prefer JRubyArt and propane users to ignore in favour of Vec2D and Vec3D ruby classes), however it is still possible to call PVector in [JRubyArt][jruby_art] and [propane][propane]. See JRubyArt sketch and output below:-

```ruby
attr_reader :pvec, :vec, :pvec2

def settings
  size 200, 200
end

def setup
  sketch_title 'Fred'
  @pvec = Java::ProcessingCore.PVector.new.tap do |v|
           v.x = 3
           v.y = 6
         end
  @vec = Vec2D.new(pvec) # this is possible via duck-typing
  # we need to know about both classes to do the reverse
  # first we return vec as an Array, then splat to produce 3 args
  @pvec2= Java::ProcessingCore.PVector.new(*(vec.to_a))
end

def draw
  p pvec
  puts pvec
  puts vec
  puts pvec2
  no_loop
end

```

A snapshot of running sketch

![fred.png]({{site.github.url}}/assets/fred.png)

What this sketch demonstrates is that you can tap into a java class. It is possible via duck-typing to create (with no ceremony) a Vec2D instance from a PVector, it requires more ceremony but it is possible to create a PVector instance from Vec2D instance. For some reason PVector to String returns an array of java float.

[propane]:https://ruby-processing.github.io/propane/
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
