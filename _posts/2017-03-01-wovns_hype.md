---
layout: post
title: "WOVNS pattern using hype"
date: 2017-03-01 00:30:00
categories: jruby_art update
keywords: jruby_art, propane, wovns, hype
---
### WOVNS

[WOVNS][wovns] is a San Francisco based company that is a textile studio and technology platform, giving designers access to the means of textile production.  Working with US textile manufacturers, they have developed a patent pending system for jacquard looms that enables textile production in quantities as small as a single yard. Customers simply access their selection of colors and qualities, apply them to their own designs. On their site is a tutorial by [David A. Mellis][mellis] that shows how to create processing sketches that you can use to create WOVNS compatible designs see [examples on github][github].

### Ruby versions

The latest [JRubyArt][jruby_art] and [propane][propane] versions feature example sketches that create WOVNS compatible output. See [JRubyArt-examples][jruby_art_examples] and [propane-examples][examples]

### Color palettes

There are some restrictions regarding color palettes that you may use for your designs, so you should probably decide what color palette (and quality) suits you to start with eg [Spectrum 18][divan]. We can deal with such web palettes quite easily in JRubyArt and propane.

### An experimental design using hype library

The sketch below uses Joshua Davis hype library with [Spectrum 18][divan] and will produce a different result each time you run it (hence use of a timestamp).

```ruby
# divan quality, spectrum-18 PALETTE
# design created using Joshua Davis hype library
load_library :hype
include_package 'hype'
# Access through Hype namespace
module Hype
  java_import 'hype.extended.colorist.HColorPool'
  java_import 'hype.extended.layout.HGridLayout'
end

PALETTE = %w(#B83D4E #B56A8C #4688B5 #A53643 #5869A0 #487A9C #95577E #302D32 #0C0000 #020100 #070707)

def settings
  size(2400, 6372)
end

def setup
  sketch_title 'Hype WOVNS Color Pool'
  H.init(self)
  H.background(color('#302D32'))
  colors = Hype::HColorPool.new(web_to_color_array(PALETTE))
  pool = HDrawablePool.new(15_876)
  pool.auto_add_to_stage
      .add(HRect.new(50))
      .layout(Hype::HGridLayout.new.start_x(0).start_y(0).spacing(50, 50).cols(126))
      .on_create do |obj|
        i = pool.current_index
        obj.no_stroke.fill(colors.get_color(i))
      end
      .request_all
  H.draw_stage
  save_frame(data_path(format("%s%s", timestamp, 'wovns.png')))
end

def timestamp
  Time.now.strftime("%Y%d%m_%H%M%S")
end
```

### Output

<img src="/assets/hype_wovns.png" />

[wovns]:https://github.com/damellis/wovns-processing-examples

[divan]:http://www.wovns.com/palettes/divan/spectrum/18/
[propane]:https://github.com/ruby-processing/propane/
[jruby_art]:https://github.com/ruby-processing/JRubyArt/
[examples]:https://github.com/ruby-processing/propane-examples/tree/master/examples/WOVNS/
[jruby_art_examples]:https://github.com/ruby-processing/JRubyArt-examples/tree/master/examples/WOVNS/
[mellis]:http://web.media.mit.edu/~mellis/
[wovns]:http://www.wovns.com/
[github]:https://github.com/damellis/wovns-processing-examples/
