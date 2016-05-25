---
layout: page
title:  "Bare Sketch"
permalink: /sketches/bare/
---

A bare sketch is similar to a sketch you would create in the processing ide, ie it does not seem to be class wrapped.  However that gets done for you at run-time, the difference from the processing sketch is that the JRubyArt sketch requires you to use the settings method (this is hidden for vanilla processing sketches run from the ide but required for eclipse / netbeans users).

Methods:-
{% highlight ruby %}
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
{% endhighlight %}

Example Usages: [JWishy][jwishy], [Drawolver][drawolver]

To create a P2D sketch use:-

{% highlight bash %}
k9 create my_sketch 300 300 p2d
{% endhighlight %}

To run such a sketch use:-

{% highlight bash %}
k9 run my_sketch.rb # uses an installed jruby or
k9 --nojruby run my_sketch.rb # to use jruby-complete
{% endhighlight %}

[jwishy]: https://github.com/ruby-processing/JRubyArt-examples/blob/master/contributed/jwishy.rb
[drawolver]: https://github.com/ruby-processing/JRubyArt-examples/blob/master/contributed/drawolver.rb
