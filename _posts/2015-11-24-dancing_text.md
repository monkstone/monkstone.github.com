---
layout: post
title:  "Geomerative Sketch"
date:   2015-11-24 16:48:13
categories: jruby_art update
---

Using the [geomerative gem][gem] in JRubyArt

```ruby
# after a sketch by Rune Skjoldborg Madsen aka runemadsen
# see https://github.com/runemadsen
require 'geomerative'

DANCE_FONT = 1

attr_reader :polygon

def settings
  size(800, 600)
end

def setup
  sketch_title 'Jiggling Font'
  color_mode(HSB, 360, 100, 100)
  frame_rate(15)
  # always initialize the library in setup
  RG.init(self)
  font = RFont.new('/usr/share/fonts/TTF/FreeSans.ttf', 600, RFont::LEFT)
  RCommand.set_segment_length(25)
  RCommand.set_segmentator(RCommand::UNIFORMLENGTH)
  # get a polygon from a single character.
  @polygon = font.to_polygon('a')
  background(360)
end

def draw
  # translate to the middle. Use plus in y because processings weird y-axis
  # convention
  translate(
    (width / 2) - (polygon.get_width / 2),
    (height / 2) + (polygon.get_height / 2)
  )
  # because a character has multiple contours (the whole in the 'a' is one,
  # the outline another), let's loop through all the contours first
  polygon.contours.each do |cur_contour|
    # for each contour, let's loop through all the points
    cur_contour.points.each do |cur_point|
      # now for each RPoint (which is a vector), make an ellipse
      # before drawing the point, let's randomize the x,y values a little bit
      cur_point.x += rand(-DANCE_FONT..DANCE_FONT)
      cur_point.y += rand(-DANCE_FONT..DANCE_FONT)
      fill(frame_count % 360, 60, 100)
      ellipse(cur_point.x, cur_point.y, 10, 10)
    end
  end
end

```

[gem]:https://ruby-processing.github.io/geomerativegem/
