---
layout: post
title: "GLVideo Capture in JRubyArt"
date: 2017-04-21 06:00:00
categories: jruby_art update
keywords: capture, jruby_art, glvideo, processing
---

### GLVideo the future video library for processing

This library uses GStreamer-1.0 (and hence possibility of hardware acceleration).  First you need to make sure you can use the library on vanilla processing, for Archlinux this may need a re-compile
see [my fork](https://github.com/monkstone/processing-glvideo). Here is a simple video capture sketch that outputs the available camera/s and the possible settings.

NB: The glvideo library is still somewhat experimental and the interface may well change (`start` is slated as an alternative to `play`).


```ruby
load_library :glvideo

include_package 'gohai.glvideo'
attr_reader :video

def settings
  size(320, 240, P2D)
end

def setup
  sketch_title 'Simple Capture'
  report_config
  #  this will use the first recognized camera by default
  @video = GLCapture.new(self)
  # you could be more specific also, e.g.
  # video = GLCapture.new(self, devices[0])
  # video = GLCapture.new(self, devices[0], 640, 480, 25)
  # video = GLCapture.new(self, devices[0], configs[0])
  video.play
end

def draw
  background 0
  video.read if video.available
  image(video, 0, 0, width, height)
end

def report_config
  devices = GLCapture.list
  puts('Devices:')
  devices.each { |dev| puts dev }
  if (0 < devices.length)
    configs = GLCapture.configs(devices[0]).to_a # ruby array
    puts('Configs:')
    configs.map { |cam| puts cam.strip }
  end
end
```
### output on my linux box with usb camera

```bash
Devices:
UVC Camera (046d:0825)
Configs:
video/x-raw, width=1280, height=960, framerate=30/1
video/x-raw, width=1280, height=960, framerate=25/1
video/x-raw, width=1280, height=960, framerate=20/1
video/x-raw, width=1280, height=960, framerate=15/1
video/x-raw, width=1280, height=960, framerate=10/1
video/x-raw, width=1280, height=960, framerate=15/2
video/x-raw, width=1280, height=960, framerate=5/1
video/x-raw, width=1280, height=720, framerate=30/1
video/x-raw, width=1280, height=720, framerate=25/1
video/x-raw, width=1280, height=720, framerate=20/1
video/x-raw, width=1280, height=720, framerate=15/1
video/x-raw, width=1280, height=720, framerate=10/1
video/x-raw, width=1280, height=720, framerate=15/2
video/x-raw, width=1280, height=720, framerate=5/1
video/x-raw, width=960, height=720, framerate=30/1
video/x-raw, width=960, height=720, framerate=25/1
video/x-raw, width=960, height=720, framerate=20/1
video/x-raw, width=960, height=720, framerate=15/1
video/x-raw, width=960, height=720, framerate=10/1
video/x-raw, width=960, height=720, framerate=5/1
video/x-raw, width=1184, height=656, framerate=30/1
video/x-raw, width=1184, height=656, framerate=25/1
video/x-raw, width=1184, height=656, framerate=20/1
video/x-raw, width=1184, height=656, framerate=15/1
video/x-raw, width=1184, height=656, framerate=10/1
video/x-raw, width=1184, height=656, framerate=5/1
video/x-raw, width=800, height=600, framerate=30/1
video/x-raw, width=800, height=600, framerate=25/1
video/x-raw, width=800, height=600, framerate=20/1
video/x-raw, width=800, height=600, framerate=15/1
video/x-raw, width=800, height=600, framerate=10/1
video/x-raw, width=800, height=600, framerate=5/1
video/x-raw, width=1024, height=576, framerate=30/1
video/x-raw, width=1024, height=576, framerate=25/1
video/x-raw, width=1024, height=576, framerate=20/1
video/x-raw, width=1024, height=576, framerate=15/1
video/x-raw, width=1024, height=576, framerate=10/1
video/x-raw, width=1024, height=576, framerate=5/1
video/x-raw, width=960, height=544, framerate=30/1
video/x-raw, width=960, height=544, framerate=25/1
video/x-raw, width=960, height=544, framerate=20/1
video/x-raw, width=960, height=544, framerate=15/1
video/x-raw, width=960, height=544, framerate=10/1
video/x-raw, width=960, height=544, framerate=5/1
video/x-raw, width=864, height=480, framerate=30/1
video/x-raw, width=864, height=480, framerate=25/1
video/x-raw, width=864, height=480, framerate=20/1
video/x-raw, width=864, height=480, framerate=15/1
video/x-raw, width=864, height=480, framerate=10/1
video/x-raw, width=864, height=480, framerate=5/1
video/x-raw, width=640, height=480, framerate=30/1
video/x-raw, width=640, height=480, framerate=25/1
video/x-raw, width=640, height=480, framerate=20/1
video/x-raw, width=640, height=480, framerate=15/1
video/x-raw, width=640, height=480, framerate=10/1
video/x-raw, width=640, height=480, framerate=5/1
video/x-raw, width=800, height=448, framerate=20/1
video/x-raw, width=800, height=448, framerate=15/1
video/x-raw, width=800, height=448, framerate=10/1
video/x-raw, width=800, height=448, framerate=5/1
video/x-raw, width=752, height=416, framerate=25/1
video/x-raw, width=752, height=416, framerate=20/1
video/x-raw, width=752, height=416, framerate=15/1
video/x-raw, width=752, height=416, framerate=10/1
video/x-raw, width=752, height=416, framerate=5/1
video/x-raw, width=640, height=360, framerate=30/1
video/x-raw, width=640, height=360, framerate=25/1
video/x-raw, width=640, height=360, framerate=20/1
video/x-raw, width=640, height=360, framerate=15/1
video/x-raw, width=640, height=360, framerate=10/1
video/x-raw, width=640, height=360, framerate=5/1
video/x-raw, width=544, height=288, framerate=30/1
video/x-raw, width=544, height=288, framerate=25/1
video/x-raw, width=544, height=288, framerate=20/1
video/x-raw, width=544, height=288, framerate=15/1
video/x-raw, width=544, height=288, framerate=10/1
video/x-raw, width=544, height=288, framerate=5/1
video/x-raw, width=352, height=288, framerate=30/1
video/x-raw, width=352, height=288, framerate=25/1
video/x-raw, width=352, height=288, framerate=20/1
video/x-raw, width=352, height=288, framerate=15/1
video/x-raw, width=352, height=288, framerate=10/1
video/x-raw, width=352, height=288, framerate=5/1
video/x-raw, width=432, height=240, framerate=30/1
video/x-raw, width=432, height=240, framerate=25/1
video/x-raw, width=432, height=240, framerate=20/1
video/x-raw, width=432, height=240, framerate=15/1
video/x-raw, width=432, height=240, framerate=10/1
video/x-raw, width=432, height=240, framerate=5/1
video/x-raw, width=320, height=240, framerate=30/1
video/x-raw, width=320, height=240, framerate=25/1
video/x-raw, width=320, height=240, framerate=20/1
video/x-raw, width=320, height=240, framerate=15/1
video/x-raw, width=320, height=240, framerate=10/1
video/x-raw, width=320, height=240, framerate=5/1
video/x-raw, width=320, height=176, framerate=30/1
video/x-raw, width=320, height=176, framerate=25/1
video/x-raw, width=320, height=176, framerate=20/1
video/x-raw, width=320, height=176, framerate=15/1
video/x-raw, width=320, height=176, framerate=10/1
video/x-raw, width=320, height=176, framerate=5/1
video/x-raw, width=176, height=144, framerate=30/1
video/x-raw, width=176, height=144, framerate=25/1
video/x-raw, width=176, height=144, framerate=20/1
video/x-raw, width=176, height=144, framerate=15/1
video/x-raw, width=176, height=144, framerate=10/1
video/x-raw, width=176, height=144, framerate=5/1
video/x-raw, width=160, height=120, framerate=30/1
video/x-raw, width=160, height=120, framerate=25/1
video/x-raw, width=160, height=120, framerate=20/1
video/x-raw, width=160, height=120, framerate=15/1
video/x-raw, width=160, height=120, framerate=10/1
video/x-raw, width=160, height=120, framerate=5/1
```
