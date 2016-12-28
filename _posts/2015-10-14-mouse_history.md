---
layout: post
title:  "Using Struct and Array in JRubyArt"
date:   2015-10-14 14:30:00
categories: jruby_art update
---

Daniel Shiffman has recently published the second edition of his [Learning Processing][shiffman] book updated for processing-3.0. He has also published the [code here][code].  This a port of his mouse history sketch, which makes use of ruby `Struct` instead of `PVector` and ruby Array in place of ArrayList.  Show your ruby chops by porting some more examples...

And send me a tweet @[monkstoneT][twitter]

Here is the sketch code:-

```ruby
# Learning Processing
# Daniel Shiffman
# https://www.learningprocessing.com

# Example 23-5: A snake following the mouse Using an Array
# of Struct (we just need something to hold x and y values)

Vect = Struct.new(:x, :y)

attr_reader :history

def setup
  sketch_title 'Mouse History'
  @history = []
end

def draw
  background(255)
  # New mouse position
  history << Vect.new(mouse_x, mouse_y)
  # Remove old ones
  history.shift if history.size > 50
  # Draw everything
  history.each_with_index do |vec, i|
    # Draw an ellipse for each element of the Array.
    no_stroke
    fill(255 - i * 5)
    ellipse(vec.x, vec.y, i, i)
  end
end

def settings
  size 300, 300
end
```

I have also ported [The Nature of Code][nature] to JRubyArt.

[nature]:https://github.com/ruby-processing/The-Nature-of-Code-for-JRubyArt
[shiffman]:https://learningprocessing.com/
[code]:https://github.com/shiffman/LearningProcessing
[twitter]:https://twitter.com/monkstoneT
