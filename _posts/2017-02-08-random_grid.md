---
layout: post
title: "Random Grid Pattern re-worked for JRubyArt"
date: 2017-02-08 13:00:00
categories: jruby_art update
keywords: jruby_art, wovns, grid, random
---
I sometimes I even surprise myself with how I can turn complicated looking vanilla processing sketches into more elegant JRubyArt.
Here is another [WOVNS][wovns] example sketch converted to JRubyArt. Here we make use of JRubyArt `web_to_color_array` to construct a dictionary of colors, we also create an array of `PShape` to make selection of shape more Object Orientated (and efficient). Further rather use rather naff points we create `ellipse` shapes with P2D that look so much sharper.

### random_grid.rb (another vanilla processing WOVNS example)

```ruby
# Original sketch by David M. Ellis
# https://github.com/damellis/wovns-processing-examples
# takes quite a long time to render
PALETTE = %w(#74AED7 #FFE4F3 #FEE597).freeze
COULEURS = %i(ciel pinking yolk).freeze
STEP = 84
attr_reader :shapes, :web

def settings
  full_screen P2D
end

def setup
  sketch_title 'Random Grid'
  @web = COULEURS.zip(web_to_color_array(PALETTE)).to_h
  no_stroke
  shape_one = createShape(ELLIPSE, 0, 0, 20, 20)
  shape_one.setFill(web[:pinking])
  shape_two = createShape(GROUP)
  one = createShape(ELLIPSE, 42, 42, 20, 20)
  one.setFill(web[:pinking])
  two = createShape(ELLIPSE, 21, 21, 20, 20)
  one.setFill(web[:yolk])
  shape_two.addChild(one)
  shape_two.addChild(two)
  shape_three = createShape(GROUP)
  base = createShape(ELLIPSE, 0, 0, 20, 20)
  base.setFill(web[:pinking])
  three = createShape(ELLIPSE, 21, 21, 20, 20)
  three.setFill(web[:pinking])
  four = createShape(ELLIPSE, 42, 42, 20, 20)
  four.setFill(web[:yolk])
  shape_three.addChild(base)
  shape_three.addChild(three)
  shape_three.addChild(four)
  shape_four = createShape(GROUP)
  five = createShape(ELLIPSE, 0, 0, 20, 20)
  five.setFill(web[:pinking])
  six = createShape(ELLIPSE, 21, 21, 20, 20)
  six.setFill(web[:pinking])
  seven = createShape(ELLIPSE, 42, 42, 20, 20)
  seven.setFill(web[:pinking])
  eight = createShape(ELLIPSE, 63, 63, 20, 20)
  eight.setFill(web[:yolk])
  shape_four.addChild(five)
  shape_four.addChild(six)
  shape_four.addChild(seven)
  shape_four.addChild(eight)
  @shapes = [shape_one, shape_two, shape_three, shape_four]
end

def draw
  background(web[:ciel]) # Sky blue
  grid(height, width, STEP, STEP) do |col, row|
    push_matrix
    translate col, row
    rkey = rand(0..6)
    shape(shapes[rkey]) if rkey < 4
    pop_matrix
  end
  save(data_path('random.png'))
  no_loop
end
```

### Output

<img src="/assets/random.png" />

[wovns]:https://github.com/damellis/wovns-processing-examples
