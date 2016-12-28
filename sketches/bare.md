---
layout: page
title:  "Bare Sketch"
permalink: /sketches/bare/
---

A bare sketch is similar to a sketch you would create in the processing ide, ie it does not seem to be class wrapped.  However that gets done for you at run-time, the difference from the processing sketch is that the JRubyArt sketch requires you to use the settings method (this is hidden for vanilla processing sketches run from the ide but required for eclipse / netbeans users).

Methods:-

```ruby
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
```

Example Usages: [JWishy][jwishy], [Drawolver][drawolver]

To create a P2D sketch use:-

```
k9 --create my_sketch 300 300 p2d # default is to use `bare` template
```

To run such a sketch use:-

```
# default is to use an installed jruby 
k9 --run my_sketch.rb # to use jruby-complete set `JRUBY: false` in `config.yml`
```

[jwishy]: https://github.com/ruby-processing/JRubyArt-examples/blob/master/contributed/jwishy.rb
[drawolver]: https://github.com/ruby-processing/JRubyArt-examples/blob/master/contributed/drawolver.rb
