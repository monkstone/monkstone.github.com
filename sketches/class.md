---
layout: page
title:  "Class Sketch"
permalink: /sketches/class/
---

An explicitly class wrapped sketch for the purists. By itself this is not that interesting, but such a sketch plays better in the [live mode][live].
Methods:-
{% highlight ruby %}
class MySketch < Processing::App
  def setup
    sketch_title 'My Sketch'
    # do static stuff here
  end
  
  def draw
    # this is the draw loop, dynamic stuff here
  end
  
  def settings
    size 300, 300 #, {optional mode}
    # full_screen, smooth etc go here
  end
end
{% endhighlight %}

To create such a sketch use:-

{% highlight bash %}
k9 create my_sketch 300 300 --wrap
{% endhighlight %}

To run such sketch in live mode use:-

{% highlight bash %}
k9 live my_sketch.rb
{% endhighlight %}


[live]:https://github.com/ruby-processing/JRubyArt/wiki/Live-Coding
