---
layout: post
title: "JRubyArt and propane on JDK12"
date: 2019-03-20 06:00:00
categories: jruby_art update
permalink: /jdk12/
keywords: ruby-processing, JRubyArt, processing, propane, developer, jdk12
---
### Work so far

I have created a jdk12 branch on github for [propane][propane]. So far it compiles and runs OK with OpenJDK12 (and the Oracle release). Also [JRubyArt][jruby_art] seems to run OK (although it was compiled on jdk8). It is however very `noisy` with much of that noise coming from jruby (jruby-9.2.6.0). I expect [vanilla processing][vanilla] with be similar with Sam Pottingers fixes.


[vanilla]:https://github.com/processing/processing/
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
[propane]:https://ruby-processing.github.io/propane/
[core]:https://github.com/ruby-processing/processing-core
