---
layout: post
title: "Video Capture Filter JRubyArt"
date: 2015-12-03 10:00:00
categories: jruby_art update
keywords: library, filter, capture, glsl, JRubyArt
permalink: _posts/video_filter
---

Here we need load two libraries the processing `video` library, and the JRubyArt art `video_event` library. The `video_event` library sole purpose is to allow you to use the java reflection method `captureEvent` those processing boys are over keen on reflection to my liking. The preferred way call the libraries is as a comma separated list of symbols _but Strings also work_.  Note that we also need to `include_package 'processing.video'` or use the fully qualified name for Capture:-

{% highlight ruby %}
load_library :video, :video_event
include_package 'processing.video'
attr_reader :cam, :my_shader

def setup
  sketch_title 'Black White Capture'
  @my_shader = load_shader(data_path('bwfrag.glsl'))
  start_capture(width, height)
end

def start_capture(w, h)
  @cam = Capture.new(self, w, h)
  cam.start
end

def draw
  image(cam, 0, 0)
  return if mouse_pressed?
  filter(my_shader)
end

def captureEvent(c)
  c.read
end

def settings
  size(640, 480, P2D)
end

{% endhighlight %}

Here is the shader filter `bwfrag.glsl` that lives in the sketch data folder

{% highlight glsl %}

#ifdef GL_ES
precision mediump float;
precision mediump int;
#endif

#define PROCESSING_TEXTURE_SHADER

uniform sampler2D texture;

varying vec4 vertColor;
varying vec4 vertTexCoord;

const vec4 lumcoeff = vec4(0.299, 0.587, 0.114, 0);

void main() {
  vec4 col = texture2D(texture, vertTexCoord.st);
  float lum = dot(col, lumcoeff);
  if (0.5 < lum) {
    gl_FragColor = vertColor;
  } else {
    gl_FragColor = vec4(0, 0, 0, 1);
  }     
}

{% endhighlight %}

[library_loader]:https://github.com/ruby-processing/JRubyArt/blob/master/lib/jruby_art/library_loader.rb
