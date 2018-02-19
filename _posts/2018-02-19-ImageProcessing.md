---
layout: post
title: "ImageProcessing library sketch in JRubyArt"
date: 2018-02-18 07:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, ImageProcessing
---
Thanks to the [ImageProcessing library][image_processing] by [Nick 'Milchreis' Müller][Milchreis] here is a another PixelFlow example translated for [JRubyArt][jruby_art], see more examples [here][jra].

```ruby
# Example for lookup tables (LUT).
# Use the mouse wheel to switch the different tables/styles
# and press the left mouse button to see the original image.
#
# Author: Nick 'Milchreis' Müller
# Translated to JRubyArt by Martin Prout

load_library :ImageProcessing
java_import 'milchreis.imageprocessing.LUT'

attr_reader :my_image, :lookuptables, :current_index, :enums

def settings
  size(550, 550)
end

def setup
  sketch_title 'LUT Example'
  @current_index = 0
  # Load image
  @my_image = load_image(data_path('example.jpg'))
  # Create an array with all lookup-tables
  # LUT Styles:
  # RETRO, CONTRAST, CONTRAST_STRONG, ANALOG1, WINTER, SPRING, SUMMER, AUTUMN
  @enums = LUT::STYLE.values
  @lookuptables = enums.map do |enum|
    LUT.load_lut(enum.java_object)
  end
  # Load one style:
  # LUT style = LUT.loadLut(LUT.STYLE.ANALOG1)
end

def draw
  return image(my_image, 0, 0) if mouse_pressed?
  image(LUT.apply(my_image, lookuptables[current_index]), 0, 0)
  fill(0)
  stylename = enums[current_index].name
  text(stylename, width / 2 - textWidth(stylename) / 2, 30)
end

def mouseWheel(event) # keep camelcase for poxy java reflection
  @current_index += 1
  @current_index = 0 if current_index >= lookuptables.length
end

```

### A snapshot of a running sketch from atom

![winter.png]({{site.github.url}}/assets/winter.png)

[image_processing]:https://github.com/Milchreis/processing-imageprocessing
[Milchreis]:https://milchreisjunkie.wordpress.com/
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
[jra]:https://github.com/ruby-processing/JRubyArt-examples/tree/master/external_library/java/image_processing
