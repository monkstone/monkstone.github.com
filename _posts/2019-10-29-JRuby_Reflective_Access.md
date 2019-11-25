---
layout: post
title: Reflective Access in JRuby
date: 2019-10-29T06:00:00.000Z
categories: jruby_art update
keywords: 'JRuby, jdk11+, development, modules, encapsulation'
---
For jdk versions greater than jdk8 running JRuby runs into a lot of reflective access warnings, but there is a solution that seems to work well on linux at least. But you need to do the following:-

1. Make sure you set JAVA_HOME environmental version (_jruby uses this to determine whether fix is required if java --version greater than version 8_)
2. Install jruby-launcher gem `sudo jgem install jruby-launcher`
3. Now many previous reflective access warnings disappear
4. Use `--add-opens` to fix remaining warnings in `.jruby.java_opts` file.
