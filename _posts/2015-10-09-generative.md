---
layout: post
title:  "Generative Gestaltung sketches in JRubyArt"
date:   2015-10-09 15:43:13
categories: jruby_art update
permalink:   _posts/timestamp
---

There is this excellent book on processing [Generative Gestaltung, ISBN: 978-3-87439-759-9][generative], and the authors have released the [code updated to run with processing-3.0+][code]. Here are a couple of the sketches translated to JRubyArt. Note the ability to use hi-dpi for retina etc in the second sketch.

Here's a chance to show your ruby chops by translating some more....see already [translated sketches here][sketches]

And send me a tweet [@monkstoneT][twitter]

{% highlight ruby %}
# p_1_0_01.rb
#
# Generative Gestaltung, ISBN: 978-3-87439-759-9
# First Edition, Hermann Schmidt, Mainz, 2009
# Hartmut Bohnacker, Benedikt Gross, Julia Laub, Claudius Lazzeroni
# Copyright 2009 Hartmut Bohnacker, Benedikt Gross, Julia Laub, Claudius Lazzeroni
#
# https://www.generative-gestaltung.de
#
# Licensed under the Apache License, Version 2.0 (the "License")
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at https://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

###########
# changing colors and size by moving the mouse
#
# MOUSE
# position x          : size
# position y          : color
#
# KEYS
# s                   : save png
# p                   : save pdf
##########
load_library :pdf
include_package 'processing.pdf'

attr_reader :save_pdf

def settings
  size(720, 720)
end

def setup
  sketch_title "P_1_0_01"
  @save_pdf = false
  no_cursor
end

def draw
  # this line will start pdf export, if the variable save_pdf was set to true
  begin_record(PDF, format("%s.pdf", timestamp)) if (save_pdf)
  color_mode(HSB, 360, 100, 100)
  rect_mode(CENTER)
  no_stroke
  background(mouse_y/2, 100, 100)
  fill(360 - mouse_y / 2, 100, 100)
  rect(360, 360, mouse_x + 1, mouse_x + 1)
  # end of pdf recording
  if (save_pdf)
    @save_pdf = false
    end_record
  end
end

def key_pressed
  case key
  when 's', 'S'
    save_frame(format("%s%s", timestamp, "_##.png"))
  when 'p', 'P' 
    @save_pdf = true
  end
end

def timestamp
  Time.now.strftime("%Y%d%m_%H%M%S")
end
{% endhighlight %}

{% highlight ruby %}
# m_3_1_01.rb
#
# Generative Gestaltung, ISBN: 978-3-87439-759-9
# First Edition, Hermann Schmidt, Mainz, 2009
# Hartmut Bohnacker, Benedikt Gross, Julia Laub, Claudius Lazzeroni
# Copyright 2009 Hartmut Bohnacker, Benedikt Gross, Julia Laub, Claudius Lazzeroni
#
# https://www.generative-gestaltung.de
#
# Licensed under the Apache License, Version 2.0 (the "License")
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at https://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

###########
# This sketch is greatly simplified in JRubyArt thanks to ArcBall...
# Rotate the grid by dragging the mouse
# Use mousewheel to zoom
##########
XCOUNT = 4
YCOUNT = 4

def settings
  size(400, 400, P3D)
  # pixel_density(2) # here for folks with hi-dpi display eg retina
  smooth(8)
end

def setup
  ArcBall.init(self)
  sketch_title "M_3_1_01"
  fill(255)
  stroke_weight(1 / 80.0)
end

def draw
  background(255)
  scale(40)
  # Draw Mesh
  YCOUNT.times do |y|
    begin_shape(QUAD_STRIP)
    XCOUNT.times do |x|
      vertex(x, y, 0)
      vertex(x, y + 1, 0)
    end
    end_shape
  end
end
{% endhighlight %}

[generative]:https://www.generative-gestaltung.de
[code]:https://www.generative-gestaltung.de/code
[twitter]:https://twitter.com/monkstoneT
[sketches]:https://github.com/ruby-processing/Generative-Design-Code-in-Ruby
