---
layout: post
title: "Using PixelFlow library in JRubyArt and propane"
date: 2017-10-10 07:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, propane, jruby, PixelFlow
---
The most recent releases of [JRubyArt][jra] and [propane][propane] feature sketches using the [PixelFlow][pixgit] library for processing by [Thomas Diewald][diewald]. Some of the sketches are based on classic [ShaderToy Demos][shadertoy], and demonstrate the sort of graphics performance not normally associated with ruby. For JRubyArt you just need to install the PixelFlow and associated libraries (`controlP5` and `peasycam`) from the vanilla processing ide. For propane it is a little more complicated and the libraries require a [manual install][libraries], and manual updating thereafter (the biggest drawback of this model), potentially this could be avoided by creating gem versions of the libraries as we have for [toxiclibs][toxiclibs], [geomerative][geomerative] and [wordcram][wordcram].

[pixgit]:https://github.com/diwi/PixelFlow
[diewald]:http://thomasdiewald.com/blog/
[jra]:https://github.com/ruby-processing/JRubyArt
[propane]:https://github.com/ruby-processing/propane
[shadertoy]:https://www.shadertoy.com/
[libraries]:https://ruby-processing.github.io/propane/contributed
[toxiclibs]:https://github.com/ruby-processing/toxicgem/
[geomerative]:https://ruby-processing.github.io/geomerativegem/
[wordcram]:https://ruby-processing.github.io/WordCram/
