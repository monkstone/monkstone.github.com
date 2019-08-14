---
layout: post
title: Strong Encapsulation
date: 2019-08-14T06:00:00.000Z
categories: jruby_art update
keywords: 'JRubyArt, jdk11, development'
---

Since jdk9 there has been a move toward strong encapsulation in java, however in order not to break existing code this has been somewhat relaxed. The exceptions are some internal and proprietary classes that were never intended to be generally available (includes some apple code which messes with original processing code on MacOS). This is detailed in [jep-261][261] and [jep-260][260] and explained [here][mvpjava] and somewhat lucidly by [Mark Rheinhold][reinhold].

What this means in practice for JRubyArt is that since jdk9, running sketches has become a lot more noisy (illegal reflective access warnings). With the latest development version of JRubyArt-2.0 (JRuby-9.2.8.0 and jdk12) I have started to explore these warnings, and it is easy as creating a 'java_args.txt' file in data folder (as sub-folder of the running sketches). I have published my results here:-

1. A simple [java 2d sketch][java2d]
2. A simple [processing P2D sketch][p2d] (jogl)
3. A simple [processing P3D sketch][p3d] (jogl)
4. A swing [control panel sketch][swing]

Possible way around includes `--add-exports`, `--add-opens`, `--add-modules`, `--add-reads` for the specific modules at runtime (you could try this in `java_args.txt`).

[260]: http://openjdk.java.net/jeps/260
[261]: http://openjdk.java.net/jeps/261
[java2d]: http://ruby-processing.github.io/jdk12/simple/
[mvpjava]: http://mvpjava.com/java-9-modules-strong-encapsulation/
[p2d]: http://ruby-processing.github.io/jdk12/p2d_sketch/
[p3d]: http://ruby-processing.github.io/jdk12/p3d_sketch/
[reinhold]: https://www.voxxed.com/2016/11/problem-modules-reflective-access/
[swing]: http://ruby-processing.github.io/jdk12/jwishy/
