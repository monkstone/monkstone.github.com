---
layout: page
title:  "Class Sketch"
permalink: /sketches/class/
---

An explicitly class wrapped sketch for the purists. By itself this is not that interesting, but such a sketch plays better in the [live mode][live].
Methods:-
```ruby
class MySketch < Processing::App
  def setup
    sketch_title 'My Sketch'
    # do static stuff here
  end
  
  def draw
    # this is the draw loop, dynamic stuff here
  end
  
  def settings
    size 300, 300 # or full_screen
    # pixel_density(density) # density = 2 for hi dpi display (eg retina)
    # smooth # goes here
  end
end
```

To create such a sketch use:-

```
k9 --create my_sketch 300 300 # with `template: class` in config.yml
```

To run such sketch in live mode use:-

```
k9 --live my_sketch.rb
```


[live]:https://github.com/ruby-processing/JRubyArt/wiki/Live-Coding
