---
layout: post
title: Reflective Access in JRubyArt
date: 2019-09-10T06:00:00.000Z
categories: jruby_art update
keywords: 'JRubyArt, jdk11, development, modules, encapsulation'
---
Since jdk9 running sketches in JRubyArt generate `WARNING:` of illegal reflective access, that will be prohibited in future versions of jdk in line with a move to stronger encapsulation see [here][260] and [here][261]. Here is a table summarising those warnings.

# Methods and fields that request reflective access by JRubyArt

<style type="text/css">
td {
      color: blue;
      background-color: yellow;
      padding: 5px;
      border: 2px solid black;
}
 </style>

requiring class                       | reflective method / field
------------------------------------- | ----------------------------------------
com.headius.backport9.modules.Modules | java.lang.Object.finalize()
com.headius.backport9.modules.Modules | java.io.FileDescriptor.fd
com.headius.backport9.modules.Modules | java.awt.Component.paramString()
                                      |
jogamp.opengl.awt.Java2D$2            | sun.java2d.opengl.OGLUtilities.UNDEFINED
jogamp.nativewindow.jawt.JAWTUtil$1   | sun.awt.SunToolkit.awtLock()

The `Object.finalize()` method and `file descriptor` field are probably JRuby specific. I suspect access to `Component.paramSring()` method might also be required by processing. The last two are required by `jogl` and needed by `P2D` and `P3D` sketches. Here are the `--add-opens` to add to `java_args.txt` to suppress the warnings in JRubyArt and propane sketches.

1. --add-opens java.base/java.lang=ALL-UNNAMED
2. --add-opens java.base/java.io=ALL-UNNAMED
3. --add-opens java.desktop/java.awt=ALL-UNNAMED
4. --add-opens java.desktop/sun.java2d.opengl=ALL-UNNAMED
5. --add-opens java.desktop/sun.awt=ALL-UNNAME

It should be possible to replace `ALL-UNNAMED` with `jruby.dist` for the first 3, but that depends on the `jruby.dist` module being recognized (currently it is only an explicitly named automatic module ie has an `AutomaticModuleName:` entry in the Manifest in the `jruby.jar`). There is the possibility of doing a global fix in JRubyArt (not propane) by adding the `--add-opens` to the `java_args:` in `~/.jruby_art/config.yml`

[260]: http://openjdk.java.net/jeps/260
[261]: http://openjdk.java.net/jeps/261
