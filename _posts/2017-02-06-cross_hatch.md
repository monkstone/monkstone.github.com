---
layout: post
title: "Fabric Patterns in JRubyArt"
date: 2017-02-06 13:00:00
categories: jruby_art update
keywords: jruby_art, wovns, grid
---
I sometimes I even surprise myself with how I can turn complicated looking vanilla processing sketches into more elegant JRubyArt.
Here is [WOVNS][wovns] example sketch converted to JRubyArt.

### cross_hatch.rb (vanilla processing WOVNS example)

```ruby
# Original sketch by David M. Ellis
# https://github.com/damellis/wovns-processing-examples
# Divan Quality, Landscape 1 Palette
# This draws a cross-hatch pattern, i.e. a grid of cells, each of which contains
# multiple lines. The lines in each cell alternate between horizontal and vertical.

# the number of stripes to draw in each cell of the grid
NUM_STRIPES = 5

def settings
  size(2400, 6372) # 13.5" x 36", 177 DPI
  no_smooth
end

def setup
  background(color('#050406')) # Black Satin
  stroke(color('#C2C2BF')) # Pearl
  stroke_weight(10) # draw lines 10 pixels thick
  # the width and height of each cell of the grid
  w = width / 18.0
  h = height / 48.0
  # the distance between stripes
  w2 = w / NUM_STRIPES
  h2 = h / NUM_STRIPES  
  grid(width, height, w, h) do |row, col| # JRubyArt grid method
    (0..NUM_STRIPES).each do |i|
      # this will be true for every other cell in the grid
      if (row % NUM_STRIPES + col % NUM_STRIPES).zero?
        # horizontal lines
        line(col + w2 / 2, row + i * h2 + h2 / 2,
        (col + 1) - w2 / 2, row + i * h2 + h2 / 2)
      else
        # vertical lines
        line(col + i * w2 + w2 / 2, row + h2 / 2,
        col + i * w2 + w2 / 2, (row + 1) - h2 / 2)
      end
    end
  end
  save(data_path('hatch.png'))
end
```

### Output

<img src="/assets/hatch.png" />

[wovns]:https://github.com/damellis/wovns-processing-examples
