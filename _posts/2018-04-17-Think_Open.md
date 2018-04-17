---
layout: post
title: "Think Open"
date: 2018-04-17 06:00:00
categories: jruby_art update
permalink: /think_open/
keywords: opportunities, processing, propane, developer, jdk9
---
### Experimental Release MacOS ###

I have just released an experimental version of propane, that includes an integrated propane-core see `propane-2.8.0.pre-java`. I have removed processing javafx which offends because it calls proprietary code, I have also removed vanilla processing `ThinkDifferent` for the same reason. It is just possible that propane continues to work on MacOS, I am looking for feedback as I do not have access to MacOS. If it works passably well then this is the future, if not the branch could be pruned to be pure linux and hence lay foundation for a raspberryPI version.

### Linux ###

This pre-release should just work on linux and has the advantage of virtually zero dependencies apart from jars available from maven central, it should be quite easy to hack the code to make it compatible with raspberryPI.

### Compiling with jdk9 ###

Can be compiled with jdk9 but it's noisy and not currently recommended:-

```bash
[tux@monkstone ~]$ java -version
Picked up _JAVA_OPTIONS: -Dswing.aatext=true
openjdk version "9.0.4"
OpenJDK Runtime Environment (build 9.0.4+11)
OpenJDK 64-Bit Server VM (build 9.0.4+11, mixed mode)
[tux@monkstone ~]$ propane --version
Picked up _JAVA_OPTIONS: -Dswing.aatext=true
Propane version 2.8.0.pre
JRuby version 9.1.16.0

```

### Help ###

Beyond testing it would be nice to have some contributions from MacOS or raspberyPI users (fork the [think_open][propane] branch). See [developing][developing]


[developing]:{{ github.site.url }}/developing/
[propane]:https://ruby-processing.github.io/propane/
