---
layout: post
title: "Export Videos from JRubyArt"
date: 2017-01-27 05:00:00
categories: jruby_art update
keywords: jruby_art, ffmpeg, processing, animation
---
[Abe Pazos aka hamoid][hamoid] has produced a processing library that makes it very easy to export processing sketches as videos (essentially it is a wrapper for ffmpeg, so you could easily achieve more customised videos yourself, but using the library makes it quick and easy). Get the [library here][here] or from [github][github]. For JRubyArt just follow the instructions for installing the vanilla processing version, then you can 'load the library' in the normal way, not forgetting to 'include_package'. For your convenience here is the basic example I have translated for you.

### basic_example.rb (JRubyArt Sketch)
```ruby
load_library :VideoExport
include_package 'com.hamoid'

# Press 'q' to finish saving the movie and exit.

# In some systems, if you close your sketch by pressing ESC,
# by closing the window, or by pressing STOP, the resulting
# movie might be corrupted. If that happens to you, use
# video_export.end_movie like you see in this example.

# In some systems pressing ESC produces correct movies
# and .end_movie is not necessary.
attr_reader :video_export

def settings
  size(600, 600)
end

def setup
  sketch_title 'Basic Example'
  @video_export = VideoExport.new(self)
  video_export.start_movie
end

def draw
  background(color('#224488'))
  rect(frame_count * frame_count % width, 0, 40, height)
  video_export.save_frame
end

def key_pressed
  return unless (key == 'q')
  video_export.end_movie
  exit
end

```

[hamoid]:https://github.com/hamoid
[github]:https://github.com/hamoid/video_export_processing
[here]:http://funprogramming.org/VideoExport-for-Processing/
