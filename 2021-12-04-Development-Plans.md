---
layout: post
title: "Instance Of Pattern Matching"
date: 2021-10-16 06:00:00
categories: jruby_art update
keywords: JRuby, extension, java, polyglot, maven, instanceof, jdk17
---

#### Subtle Changes for Vec2D ####

Recent discussions about vectors and the py5 project persuaded me to prefer wedge product over cross product for 2D vectors (since by definition there is no such thing as cross product that returns a scalar). In the first instance I will keep the cross product in the api, but it will be deprecated  in favor of the `^` (wedge product operator). See [PiCrate example][picrate-example]


[picrate-example]:https://github.com/ruby-processing/picrate-examples/blob/master/demo/library/circle/lib/t_points.rb
