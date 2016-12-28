---
layout: post
title: "Literate Colors with JRubyArt"
date: 2016-04-22 06:31:00
categories: jruby_art update
keywords: literate, color, coluer, JRubyArt
---

In a previous post I suggested we could use a hash to use web colors in a more literate way, here is a version for a Francophone in [JRubyArt][jruby_art]. Note that we need to convert webstrings to an int within `setup` since `web_to_color_array` is a [JRubyArt][jruby_art] method. We could also have mapped the web strings to a color array but this convenience method is more efficient.

### couluers.rb ###

```ruby
# encoding: utf-8
COULEURS = %i(jaune rose rouge bleu vert gris noir violet blanc).freeze
PALETTE = %w(
  #FFFF00
  #FF007F
  #FF0000
  #0000FF
  #00CC00
  #736F6E
  #000000
  #EE82EE
  #FFFFFF
).freeze

attr_reader :web

def setup
  sketch_title 'Literate Web Colors'
  @web = COULEURS.zip(web_to_color_array(PALETTE)).to_h
  no_loop
end

def draw
  background web[:gris]
  fill(web[:jaune])
  ellipse(50, 50, 50, 50)
  fill(web[:rose])
  ellipse(150, 50, 50, 50)
  fill(web[:rouge])
  ellipse(250, 50, 50, 50)
  fill(web[:bleu])
  ellipse(50, 150, 50, 50)
  fill(web[:vert])
  ellipse(150, 150, 50, 50)
  fill(web[:noir])
  ellipse(250, 150, 50, 50)
  fill(web[:violet])
  ellipse(150, 250, 50, 50)
  fill(web[:blanc])
  ellipse(250, 250, 50, 50)
end

def settings
  size 300, 300, FX2D
  smooth 4
end

```

<img src="/assets/couleurs.png" />

[jruby_art]:https://ruby-processing.github.io/index.html

