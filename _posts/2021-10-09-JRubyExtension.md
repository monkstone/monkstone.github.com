---
layout: post
title: "Extending JRuby, Compile and Jar Java Extension Code"
date: 2021-10-09 06:00:00
categories: jruby_art update
keywords: JRuby, extension, java, polyglot, maven, compiling, jar
---


## Do you want to create a JRuby extension? ##

Previously you may have seen guides by [Yoko Harada (yokolet)][yokolet] and [James Coglan][coglan] which suggest you use the [Rake-Compiler][rake] to compile your code. However those recommendations were made before the [polyglot maven compiler][polyglot] existed, which is now the recommended way to compile and jar your ruby code. You can now write your pom file in ruby (and get regular pom.xml emitted useful when working with a java ide such as netbeans). For a basic example see [jruby-examples][basic] which demonstrates how to create a ruby class and ruby module in java. The article by [James Coglan][coglan] remains in interesting, as it shows how you should create a java extension of a gem with an existing MRI extension. The beauty of maven is that you can pull in jar depencies from maven central including jruby-base.jar which is the recommended jruby dependency when compilig jruby extension (_was jruby.jar but that may cause dependency conflicts_).


[yokolet]:https://yokolet.blogspot.com/2011/06/extending-jruby-compile-and-jar-java.html
[coglan]:https://blog.jcoglan.com/2012/08/02/your-first-ruby-native-extension-java/

[rake]:https://github.com/rake-compiler/rake-compiler
[polyglot]:https://github.com/takari/polyglot-maven
[basic]:https://github.com/jruby/jruby-examples/tree/master/extensions/basic/jruby-ext
