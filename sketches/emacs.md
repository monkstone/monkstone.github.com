---
layout: page
title:  "Sketch for Emacs"
permalink: /sketches/emacs/
---

An explicitly class wrapped sketch for use in [emacs][]/netbeans will run with `jruby` does not require `k9 run`.

Methods:-
{% highlight ruby %}
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
    size 300, 300
    # smooth # here
  end
end

MySketch.new unless defined? $app
{% endhighlight %}

To create such a sketch use:-

{% highlight bash %}
k9 create my_sketch 300 300 --emacs
{% endhighlight %}

[emacs]:https://github.com/ruby-processing/JRubyArt/wiki/Using-emacs-as-your-JRubyArt-Ide
