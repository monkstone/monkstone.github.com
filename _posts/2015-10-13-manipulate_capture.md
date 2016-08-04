---
layout: post
title:  "Manipulate Video Capture in JRubyArt"
date:   2015-10-13 14:48:00
categories: jruby_art update
---

Daniel Shiffman has recently published the second edition of his [Learning Processing][shiffman] book updated for processing-3.0. He has also published the [code here][code].  This a port of his Manipulate video image sketch, which makes use of JRubyArt control panel to manipulate the video image. Show your ruby chops by porting some more examples...


And send me a tweet @[monkstoneT][twitter]

Here is the sketch code:-

{% highlight ruby %}
# Learning Processing
# Daniel Shiffman
# https://www.learningprocessing.com

# Example 16-2: Manipulate video image

# Import the video library (and for JRubyArt control_panel and video_event).

load_libraries :control_panel, :video, :video_event
include_package 'processing.video'

# Variable for capture device
attr_reader :angle, :blue, :green, :hide, :high, :panel, :red, :video, :wide

def setup
  sketch_title 'Manipulate Capture'
  @video = Capture.new(self, 320, 240)
  # Start capturing the images from the camera
  video.start
  control_panel do |c|
    c.title = 'Control Panel'
    c.look_feel 'Nimbus'
    c.slider :red, 0..255, 255
    c.slider :blue, 0..255, 255
    c.slider :green, 0..255, 255
    c.slider :angle, -PI..PI, 0
    c.slider :wide, 0..320, 320
    c.slider :high, 0..240, 240
    @panel = c
  end
end

def draw
  background(255)
  # only make control_panel visible once, or again when hide is false
  unless hide
    @hide = true
    panel.set_visible(hide)
  end
  # Tinting using sliders
  tint(red, green, blue)
  # A video image can also be moved, rotated, tinted, resized just as with a PImage.
  translate(width / 2, height / 2)
  image_mode(CENTER)
  rotate(angle) # angle from slider
  image(video, 0, 0, wide, high) # width and height from slider
end

# This is a 'java reflection' method in processing, so keep the camel-case
# and load the :video_event library (rpextras.jar) in JRubyArt to use.
def captureEvent(c)
  c.read
end

def settings
  size(320, 240)
end
{% endhighlight %}

I have also ported [The Nature of Code][nature] to JRubyArt.

[nature]:https://github.com/ruby-processing/The-Nature-of-Code-for-JRubyArt
[shiffman]:https://learningprocessing.com/
[code]:https://github.com/shiffman/LearningProcessing
[twitter]:https://twitter.com/monkstoneT
