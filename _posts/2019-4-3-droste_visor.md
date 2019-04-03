---
layout: post
title: "Filters with Visor"
date: 2019-4-3 06:00:00
categories: jruby_art update
keywords: jruby_art, Visor, prototype, vjaying
---

Getting to grips with coding for Visor, and yes JRubyArt is a great prototyping tool. Here is this [JRubyArt glsl filter sketch](https://github.com/ruby-processing/filters4jruby_art/blob/master/image_filtering/droste.rb) translated for Visor.
```ruby
# ===== Default : Default
@home = ENV['HOME']


@origin = Time.now
@my_image  = load_image(File.join(@home, 'texture.jpg'))
@my_filter = load_shader(File.join(@home,'droste.glsl'))
@my_filter.set('sketchSize', width.to_f, height.to_f)


def time
  Time.now - origin
end

def draw
  background(0)
  # Draw the image on the scene
  image(my_image, 0, 0)
  my_filter.set('globalTime', time)
  filter(my_filter)
end


```

Screenshot:-

![droste.png](https://github.com/SableRaf/Filters4Processing/blob/master/screenshots/Droste.png)
