---
layout: post
title: "GiCentre ColourTable Example in JRubyArt"
date: 2019-03-16 06:00:00
categories: jruby_art update
keywords: jruby_art, animation
---

### JRubyArt Sketch
I was looking for a example colour table and I found [this example](https://www.gicentre.net/utils/colour) by Jo Wood.

```ruby
# Author Jo Wood. Adapted for JRubyArt by Martin Prout 2019
load_library 'gicentreUtils'
java_import 'org.gicentre.utils.colour.ColourTable'

attr_reader :table1, :table2, :table3 # Colour tables to use.

def settings
  size(500, 250)
end

def setup
  sketch_title('GiCentre ColourTable Example in JRubyArt')
  # Create a continuous Brewer colour table (YlOrBr6).
  @table1 = ColourTable.new
  table1.add_continuous_colour_rule(0.5 / 6, 255, 255, 212)
  table1.add_continuous_colour_rule(1.5 / 6, 254, 227, 145)
  table1.add_continuous_colour_rule(2.5 / 6, 254, 196, 79)
  table1.add_continuous_colour_rule(3.5 / 6, 254, 153, 41)
  table1.add_continuous_colour_rule(4.5 / 6, 217, 95, 14)
  table1.add_continuous_colour_rule(5.5 / 6, 153, 52, 4)
  # Create a preset colour table and save it as a file
  @table2 = ColourTable.get_preset_colour_table(ColourTable::IMHOF_L3, 0, 1)
  ColourTable.write_file(table2, create_output(data_path('imhofLand3.ctb')))
  # Read in a colour table from a ctb file.
  @table3 = ColourTable.read_file(create_input(data_path('imhofLand3.ctb')))
end

def draw
  background(255)
  # Draw the continuous Brewer colour table.
  draw_color_table(table1, 0.001, 10)
  # Draw the discrete version of the Brewer colour table.
  draw_color_table(table1, 1 / 6.0, 70, true)
  # Draw the preset colour table.
  draw_color_table(table2, 0.001, 130)
  # Draw the colour table loaded from a file.
  draw_color_table(table3, 1 / 6.0, 190)
end

def draw_color_table(table, increment, height, outline = false)
  (0..1.0).step(increment) do |i|
    fill(table.find_colour(i))
    outline ? stroke(0, 150) : stroke(table.find_colour(i))
    rect(width * i, height, width * increment, 50) unless outline
    rect(width * i, height, width * increment, 50, 2) if outline
  end
end



```

### The Sketch

<img src="/assets/colour_table.png" />
