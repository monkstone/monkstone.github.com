---
layout: post
title: "Using gicentreUtils Library"
date: 2016-02-12 15:32:00
categories: jruby_art update
keywords: library, gicentre, chart, JRubyArt
---

Here is an example sketch by Jo Wood, that has been refactored for [JRubyArt][jruby_art]. Install the gicentreUtils library from the processing-3.0.1 ide.

### bar_chart.rb ###

{% highlight ruby %}
def settings
  size(800, 300)
  smooth 8
end

def setup
  sketch_title 'Bar Chart in JRubyArt'
  no_loop
  @title_font = create_font('helvetica', 22)
  @small_font = create_font('helvetica', 12)
  text_font(small_font)
  @bar_chart = BarChart.new(self)
  bar_chart.data =
    [
      2_462, 2_801, 3_280, 3_983, 4_490, 4_894, 5_642, 6_322, 6_489, 6_401,
      7_657, 9_649, 9_767, 12_167, 15_154, 18_200, 23_124, 28_645, 39_471
    ].to_java(:float)
  bar_chart.bar_labels =
    %w(
      1830 1840 1850 1860 1870 1880 1890 1900 1910 1920
      1930 1940 1950 1960 1970 1980 1990 2000 2010
    )
  bar_chart.bar_colour = color(200, 80, 80, 100)
  bar_chart.bar_gap = 2
  bar_chart.value_format = '$###,###'
  bar_chart.show_value_axis(true)
  bar_chart.show_category_axis(true)
end

# ------------------ Processing draw loop -------------------

# Draws the graph in the sketch.
def draw
  background(255)
  bar_chart.draw(10, 10, width - 20, height - 20)
  fill(120)
  text_font(title_font)
  text(TITLE, 70, 30)
  vertical_spacing = 30 + text_ascent # add the current text height
  text_font(small_font)
  text(LEGEND, 70, vertical_spacing)
end

{% endhighlight %}

<img src="/assets/chart.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
