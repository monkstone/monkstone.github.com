---
layout: post
title: "Demonstrating Use of Random Trigger from Hype in JRubyArt"
date: 2016-05-15 05:03:00
categories: jruby_art update
keywords: hype, callback, random trigger, JRubyArt
---

Using Hype Random Trigger Callback in [JRubyArt][jruby_art]

### Create a palette of web-colors ###

First create PALETTE an array of web color strings and an array of symbols to use as keys.

{% highlight ruby %}
PALETTE = %w(#242424 #999999 #202020).freeze
KEY = %i(background stroke fill).freeze
{% endhighlight %}

Next within setup (to use `web_to_color_array` method) create a hash (dictionary for pythonistas) of color ints.

{% highlight ruby %}
col = KEY.zip(web_to_color_array(PALETTE)).to_h
{% endhighlight %}

Then use the colors in your sketch `col[:background]` a bit of an overkill in this sketch. You could use `color('#242424')` instead, note we must quote the web color string in ruby, and use the JRubyArt `color` method to return a color int. _Vanilla-processing does some pre-processing for you._

### Create a callback on the ran_trig object in JRubyArt ###

Much, simpler than in vanilla processing see [original][hype]. Here we replace the anonymous `new HCallback()` class instance with a ruby block.

{% highlight ruby %}
ran_trig.callback do
  d.loc(
    rand(50..width - 50),
    rand(50..height - 50)).size( 50 + (rand(3) * 50))
                          .anchor_at(H::CENTER)
                          .rotation_rad(rand(TWO_PI)
  )
end
{% endhighlight %}

Putting it all together, unfortunately output below just a snapshot here, the [original][hype] features a processing.js version:-

### random_trigger.rb ###

{% highlight ruby %}
# encoding: utf-8
load_library :hype
include_package 'hype'
java_import 'hype.extended.behavior.HRandomTrigger'

PALETTE = %w(#242424 #999999 #202020).freeze
KEY = %i(background stroke fill).freeze

def settings
  size(640,640)
end

def setup
  sketch_title 'Random Trigger'
  col = KEY.zip(web_to_color_array(PALETTE)).to_h
  H.init(self)
  H.background(col[:background])
  # Create a new randTrigger with a 1 in 15 chance
  # of triggering everytime H.draw_stage is called.
  ran_trig = HRandomTrigger.new(1.0 / 15)
  # ranTrig = HRandomTrigger.new.chance( 1.0 / 15 ) # same as above

  d = H.add(
    HRect.new
         .rounding(8))
         .stroke_weight(2)
         .stroke(col[:stroke])
         .fill(col[:fill])
         .loc_at(H::CENTER)
         .size(50 + (rand(3) * 50))
         .anchor_at(H::CENTER
  )

  # Setting the callback is similar to HTimer, we replace
  # an anonymous class instance with a block in JRubyArt

  ran_trig.callback do
    d.loc(
      rand(50..width - 50),
      rand(50..height - 50)).size( 50 + (rand(3) * 50))
                            .anchor_at(H::CENTER)
                            .rotation_rad(rand(TWO_PI)
    )
  end
end

def draw
  H.draw_stage
end
{% endhighlight %}


<img src="/assets/random_trigger.png" />

[jruby_art]:https://ruby-processing.github.io/index.html

[hype]:http://www.hypeframework.org/examples/HRandomTrigger/example_001/index.html