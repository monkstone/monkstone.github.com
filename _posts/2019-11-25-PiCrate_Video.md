---
layout: post
title: Picrate-1.2.1 Raspbian Stretch
date: 2019-10-12T06:00:00.000Z
categories: jruby_art update
keywords: 'jruby, picrate, processing, RaspberryPI, Oracle, java'
---

![capture_test.png]({{site.github.url}}/assets/capture_test.png)

```ruby
#!/usr/bin/env jruby
require 'picrate'

# Use this sketch to find available cameras you should set size to match
# your web camera eg 720P camera is 960, 720. Not tested with the
# RaspberryPI Camera module
class CaptureTest < Processing::App

  load_library :video # install latest beta version
  java_import 'processing.video.Capture'

  attr_reader :cam, :cameras

  def setup
    sketch_title 'Capture Test'
    @cameras = Capture.list
    fail 'There are no matching cameras.' if cameras.length.zero?
    start_capture(cameras[0])
    font = create_font('DejaVu Sans', 28)
    text_font font, 28
  end

  def start_capture(cam_string)
    # NB: camera should be initialised as follows
    @cam = Capture.new(self, width, height, cam_string)
    p format('Using camera %s', cam_string)
    cam.start
  end

  def draw
    return unless cam.available # ruby guard clause

    cam.read
    set(0, 0, cam)
    fill 255
    rect 0, height - 80, width, 50
    fill 0
    text cameras[0], 30, height - 50
  end

  def settings
    size 960, 720, P2D
  end
end

CaptureTest.new

```
