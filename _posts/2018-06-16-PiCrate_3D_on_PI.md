---
layout: post
title: "OpenGL PiCrate sketches on RaspberryPI"
date: 2018-06-16 05:00:00
categories: jruby_art update
permalink: /picrate_on_raspberrypi/
keywords: processing, jruby, PiCrate, java
---
### This should be straightforward

I originally developed PiCrate on my `amd64` linux box, all looks good (as expected, it is essentially [propane][propane] after-all), and when I got my raspberryPI, I was heady with success when I got `jwishy` etc to run. Not so fast though, opengl sketches are failing.

### Blind alleys

I tried most things, [Gottfreid Haider][haider] warns to use default display settings on PI (this is good advice but did not solve my opengl issue). I even resorted to getting [JRubyArt][jruby_art] working on raspberryPI which was never my intention, but it's there (to do that I used the processing.org raspbian image). I was still stuck when [ShadowfeindX][ShadowfeindX] requested propane to work on Windows, he'd got to work for him (and for opengl he seemed to need non-versioned jars!).

### Light bulb moment

Now it seem to be I have been lulled into false sense of security re `JOGL` and `GLUEGEN` jars, thinking I could use the latest vanilla release (2.3.2), but upon inspection the jars used in vanilla processing have been patched. This wasn't just restricted to the native binary jars, but also removing the versioning seemed to be important.

### The fix

Now this is not super convenient, and messes with my maven/gem build but this what I now do:-

Compile PiCrate (propane) jars as before
Copy and include `JOGL` and `GLUEGEN` jars from vanilla processing into gem

It would have been so much easier if processing.org used maven central.

### The joy

PiCrate 3D now works (in the limited way it can on the RaspberryPI) on the [noobs raspbian distro][noobs]:-

All you need do is install [JRuby][jruby] (it also pays to set up a local gem store). But we are not out of the woods yet [JRuby][jruby] needs fixing (or more specifically its [jnr-ffi][jnr-ffi]) to allow `jgem` install from rubygems, so we still need to download the ArcBall and PiCrate gems and do a local install.

### The proof
monjori shader

![monjori.png]({{site.github.url}}/assets/monjori.png)

joonsrender raytraced media

![balls.png]({{site.github.url}}/assets/balls.png)

Screenshots of opengl frames on raspberryPI don't work (I tried scrot and gimp)

### To Do

Tailor examples for PI, await [jnr-ffi][jnr-ffi] fix (I'm out of my depth with asm types etc). Create some cool PiCrate specific examples such as an implementation of [TechnoMachine][technomachine].

```ruby
#!/usr/bin/env jruby
require 'picrate'

class FractionSums < Processing::App
  # fractions.rb, by Martin Prout
  attr_reader :f, :two, :three, :four, :five, :six, :subtract, :multiply

  def setup
    sketch_title 'Math Blackboard'
    @f = createFont('Arial', 24, true)
    half = 1 / 2r     # since ruby 2.1.0 (and jruby-9.0.0.0)
    third = 1 / 3r
    quarter = 1 / 4r
    fifth = 1 / 5r
    sixth = 1 / 6r
    seventh = 1 / 7r
    format2 = '%s + %s                                     = %s'
    format3 = '%s + %s + %s                            = %s'
    format4 = '%s + %s + %s + %s                   = %s'
    format5 = '%s + %s + %s + %s + %s          = %s'
    format6 = '%s + %s + %s + %s + %s + %s = %s'
    sum2 =  half + third
    result2 = numeric_to_mixed_number(sum2)
    @two = format(format2, half, third, result2)
    sum3 =  half + third + quarter
    result3 = numeric_to_mixed_number(sum3)
    @three = format(format3, half, third, quarter, result3)
    sum4 =  half + third + quarter + fifth
    result4 = numeric_to_mixed_number(sum4)
    @four = format(format4, half, third, quarter, fifth, result4)
    sum5 =  half + third + quarter + fifth + sixth
    result5 = numeric_to_mixed_number(sum5)
    @five = format(format5, half, third, quarter, fifth, sixth, result5)
    sum6 =  half + third + quarter + fifth + sixth + seventh
    result6 = numeric_to_mixed_number(sum6)
    @six = format(format6, half, third, quarter, fifth, sixth, seventh, result6)
  end

  def draw
    background 10
    text_font(f, 24)
    fill(220)
    text('Math Blackboard Processing', 110, 50)
    text(two, 80, 80)
    text(three, 80, 105)
    text(four, 80, 130)
    text(five, 80, 155)
    text(six, 80, 180)
  end

  def numeric_to_mixed_number(amount)
    amount_as_integer = amount.to_i
    if (amount_as_integer != amount.to_f) && (amount_as_integer > 0)
      fraction = amount - amount_as_integer
      format('%s %s', amount_as_integer, fraction)
    else
      amount.to_s
    end
  end

  def settings
    size 700, 240
  end
end

FractionSums.new


```

![fraction_sums.png]({{site.github.url}}/assets/fraction_sums.png)

### A Sketch demonstrating ruby support for complex numbers
```ruby
#!/usr/bin/env jruby
require 'picrate'
# Mandelbrot Set example
# by Jordan Scales (http://jordanscales.com)
# Modified to use map1d (instead of map), and somewhat
# optimized (update_pixels instead of set)
# no need to loop
class Mandelbrot < Processing::App

  def setup
    sketch_title 'Mandelbrot'
    load_pixels
    no_loop
  end

  # main drawing method
  def draw
    grid(900, 600) do |x, y|
      c = Complex(
        map1d(x, (0...900), (-3..1.5)), map1d(y, (0...600), (-1.5..1.5))
      )
      # mandel will return 0..20 (20 is strong) map this to 255..0 (NB: reverse)
      pixels[x + y * 900] = color(map1d(mandel(c, 20), (0..20), (255..0)).to_i)
    end
    update_pixels
  end

  # calculates the "accuracy" of a given point in the mandelbrot set
  # how many iterations the number survives without becoming chaotic
  def mandel(z, max = 10)
    score = 0
    c = z
    while score < max
      # z = z^2 + c
      z *= z
      z += c
      break if z.abs > 2
      score += 1
    end
    score
  end

  def settings
    size 900, 600
  end
end

Mandelbrot.new
```

[haider]:https://pi.processing.org/
[technomachine]:https://github.com/mehackit/technomachine
[jnr-ffi]:https://github.com/jnr/jnr-ffi
[jruby]:http://jruby.org/download
[noobs]:https://www.raspberrypi.org/downloads/noobs/
[ShadowfeindX]:https://github.com/ShadowfeindX
[propane]:https://github.com/ruby-processing/propane
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
