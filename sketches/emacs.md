---
layout: page
title:  "Sketch for Emacs"
permalink: /sketches/emacs/
---

An explicitly class wrapped sketch for use in [emacs][]/netbeans will run with `jruby` does not require `k9 --run`.

Methods:-
```ruby
require 'jruby_art'
require 'jruby_art/app'

Processing::App::SKETCH_PATH = __FILE__

class MySketch < Processing::App
  def setup
    sketch_title 'My Sketch'
  end

  def draw

  end

  def settings
    size 300, 300 # or full_screen
    # pixel_density(density) # density = 2 for hi dpi display (eg retina)
    # smooth # goes here
  end
end

MySketch.new unless defined? $app
```

To create such a sketch use:-

```
k9 --create my_sketch 300 300 # with `template: emacs` in `config.yml`
```

[emacs]:https://github.com/ruby-processing/JRubyArt/wiki/Using-emacs-as-your-JRubyArt-Ide
