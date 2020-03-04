---
layout: post
title: Reflective Access in JRuby
date: 2020-03-04T06:00:00.000Z
categories: jruby_art update
keywords: 'JRuby, jdk11+, development, modules, encapsulation'
---
For JRuby-9.2.11.0 the reflective access warnings with jdk > 8 are mainly fixed (providing you define `JAVA_HOME`). However
for JRubyArt (PiCrate and propane) `JOGL` warnings remain for OpenGL sketches

For Debian linux, define `JAVA_HOME` in `~/.profile` for ArchLinux and others define in `~/.bashrc`. Windows and Mac users should do similar. Create a `~/.jruby.java_opts` file with following content to fix `JOGL` reflective access warnings:-


```bash
--add-opens java.base/java.lang=ALL-UNNAMED
--add-opens java.desktop/java.awt=ALL-UNNAMED
--add-opens java.desktop/sun.awt=ALL-UNNAMED
--add-opens java.desktop/sun.java2d.opengl=ALL-UNNAMED

```

NB: ALL-UNNAMED is for legacy jar access, if we had named modules we could/should be more specific
