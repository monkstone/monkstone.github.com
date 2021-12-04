---
layout: post
title: "Plans For Future Development"
date: 2021-12-04 06:00:00
categories: jruby_art update
keywords: JRuby, extension, java, polyglot, maven, instanceof, jdk17
---

#### Subtle Changes for Vec2D ####

Recent discussions about vectors and the py5 project persuaded me to prefer wedge product over cross product for 2D vectors (since by definition there is no such thing as cross product that returns a scalar). In the first instance I will keep the cross product in the api, but it will be deprecated  in favor of the `^` (wedge product operator). See [PiCrate example][picrate-example] ``:collinear?`` method.

#### Use of jdk17 ####

Initially propane will be changed to require jdk17 (probably by xmas 2017), for JRubyArt and PiCrate, I will wait to see what processing.org and jruby.org do, there will be no going back otherwise the change will be pointless (since I am currently using jdk 17 to test and run both projects)!!!

[picrate-example]:https://github.com/ruby-processing/picrate-examples/blob/master/demo/library/circle/lib/t_points.rb
