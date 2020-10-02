---
layout: post
title: "Refinements in Ruby-Processing"
date: 2020-10-02 06:00:00
categories: jruby_art update
keywords: picrate, processing, RaspberryPI4
---

Since ruby-2.4 there is a possibility of using refinements in ruby-processing projects, recently I've been working on sketches using complex numbers. It occurred to me I could use refinements (instead of monkey patching) to extend the Vec2D class support complex number operations, here is what I came up with:-
```ruby
require 'picrate'

module CVec2D
  refine Vec2D do
    def complex_mult(vec_b)
      Vec2D.new(
        self.x*vec_b.x - self.y*vec_b.y, self.x*vec_b.y + self.y*vec_b.x
      )
    end

    def inverse
      Vec2D.new(self.x, -self.y) / self.dot(self)
    end

    def complex_divide(vec_b)
      self.complex_mult(vec_b.inverse)
    end

    def complex_exp
      Vec2D.new(Math.exp(self.x) * Math.cos(self.y), Math.exp(self.x) * Math.sin(self.y))
    end

    def complex_log
      Vec2D.new(Math.log(self.mag), Math.atan2(self.y, self.x));
    end

    def complex_power(vec_b)
      self.complex_log.complex_mult(vec_b).complex_exp
    end
  end
end

class RefineVec2D
  using CVec2D
  attr_reader :vec_a, :vec_b

  def initialize
    @vec_a = Vec2D.new(3, 4)
    @vec_b = Vec2D.new(5, 6)
  end

  def test
    puts vec_a.complex_divide(vec_b)
    puts vec_a.complex_mult(vec_b)
    puts vec_a.inverse
    puts vec_a.complex_power(Vec2D.new(3, 0))
  end
end

test_refine = RefineVec2D.new
test_refine.test

```

Here is the output:-
```bash
Vec2D(x = 0.6393, y = 0.0328)
Vec2D(x = -9.0000, y = 38.0000)
Vec2D(x = 0.1200, y = -0.1600)
Vec2D(x = -117.0000, y = 44.0000)
                       
```
