---
layout: post
title:  "Sketch Watching"
date:   2015-11-18 06:45:00
categories: jruby_art update
permalink:   _posts/watch
---

### Automatically Reload your Sketch with `k9 watch`

`k9 watch` will keep an eye on the source files (*.rb and *.glsl only) of your sketch. Whenever you save a change to it, it'll reload your running code, so you can try out your ideas quickly. `k9 watch` is the best way to quickly prototype a sketch. To guard against you running watch in a top level folder we limit the number of files to `watch` to 20, but you can set a different limit in ~/.jruby_art/config.yml `MAX_WATCH: 100`. However the safest thing to do is make sure that you use watch from a custom folder to avoid `watching` non-sketch code. See Joanne Cheng video for a demo of the [ruby-processing watch mode][video].  See [Wiki][wiki] for true live mode with pry.

{% highlight bash %}
k9 create simple 300 300
k9 watch simple.rb # NB don't :!k9 watch % from vim 
{% endhighlight %}

<img src="/assets/simple.png" />

In another console (or use other editor)
{% highlight bash %}
vim simple.rb
{% endhighlight %}

{% highlight ruby %}
def setup
  sketch_title 'Simple'
end

def draw

end

def settings
  size 300, 300
  # pixel_density(2) # here for hi-dpi displays only
  # smooth # here
end
{% endhighlight %}

Add a red ellipse to draw loop and save (sketch reloads automatically)

{% highlight ruby %}
def draw
  fill 200, 0, 0
  ellipse 150, 150, 200, 100
end
{% endhighlight %}

<img src="/assets/simple0.png" />

Set background blue and save (sketch reloads automatically)

{% highlight ruby %}
def draw
  background 0, 0, 200
  fill 200, 0, 0
  ellipse 150, 150, 200, 100
end
{% endhighlight %}

<img src="/assets/simple1.png" />

__TIP__

If you declare constants in your sketch you may wish to define them this way `CONSTANT ||= 'some value'` to avoid getting the `warning: already initialized constant` when the sketch re-loads.



[video]:https://vimeo.com/88994050
[wiki]:https://github.com/ruby-processing/JRubyArt/wiki/Live-Coding


k9 create simple 300 300

def setup
  sketch_title 'Simple'
end

def draw

end

def settings
  size 300, 300
  # pixel_density(2) # here for hi-dpi displays only
  # smooth # here
end

vim simple.rb

:!k9 watch %

def setup
  sketch_title 'Simple'
end

def draw
  background 0, 0, 200
  fill 200, 0, 0
  ellipse 150, 150, 200, 100
end

def settings
  size 300, 300
  # pixel_density(2) # here for hi-dpi displays only
  # smooth # here
end



