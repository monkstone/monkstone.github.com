---
layout: post
title: "Creating and iterating a sequence of java constants"
date: 2016-11-06 04:00:00
categories: jruby_art update
keywords: java, contstants, instance_eval, jruby

---
I think I have found an elegant way to create and iterate over a sequence of java constants (provided as an interface `hype.interfaces.HConstants`) where `class H implements HConstants`.  The constants in this case are hexadecimal numbers (eg `CLEAR` = `0x00FFFFFF`). It might be even more efficient to have `FSTRING` = `Java::HypeInterfaces::HConstants::%s`?  Seems to work well in JRubyArt, is there any reason I should not promote this method?

```ruby

load_library :hype
include_package 'hype'
java_import 'hype.extended.layout.HGridLayout'
# string color array
PALETTE = %w(
  CLEAR
  WHITE
  BLACK
  LGREY
  GREY
  DGREY
  RED
  GREEN
  BLUE
  CYAN
  YELLOW
  MAGENTA
).freeze
# format string see fill below
FSTRING = 'H::%s'.freeze

def settings
  size(640, 640)
end

def setup
  sketch_title 'Color Presets'
  H.init(self)
  H.background(color('#242424'))
  grid = HGridLayout.new(3).spacing(210, 157).start_loc(10, 10)
  PALETTE.each do |col|
    H.add(HRect.new(200, 147))
     .stroke_weight(3)
     .stroke(H::BLACK, 150.0) # regular use of java constants
     .fill(instance_eval(format(FSTRING, col))) # elegant use of instance_eval?
    .loc(grid.get_next_point)
  end
end

def draw
  H.draw_stage
  no_loop
end

```


![color_presets.png]({{ github.site.url }}/assets/color_presets.png)
