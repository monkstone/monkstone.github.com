---
layout: post
title:  "Proxy Processing Library in JRubyArt"
date:   2015-12-15 00:07:34
categories: jruby_art update
keywords: abstract, reflection, library, JRubyArt
---

In vanilla processing [library developers][vanilla] are encouraged to use java reflection to access the sketch `pre`, `post`, and `draw` loops. They do this by `registering` the processing methods (some java reflection magic) with the PApplet. Until recently we needed jruby `ext` and `become_java!` to access those methods in JRuby. However now all you need to do is `load` the `library_proxy` and make your `Library` class subclass `LibraryProxy`. This is still at the development stage so the interface may not be stable. The `LibraryProxy` class is an abstract java class and you should implement `pre`, `post` and `draw` methods (empty methods are fine). Also available are
`app` to access the underlying PApplet, `background`, `fill` and `stroke`, access the rest via app see proxy_library where `app.ellipse` is used.

### The Sketch

{% highlight ruby %}
# test_proxy.rb by Martin Prout
load_library :library_proxy
require_relative 'proxy_library'

def settings
  size 300, 300
end

def setup
  sketch_title 'Test LibraryProxy'
  TestProxy.new(self) # initialize our TestProxy Library
end

def draw
  background 0, 0, 200	
  fill 200, 0, 0
  ellipse 150, 150, 200, 100
end
{% endhighlight %}

### The Library

{% highlight ruby %}
# proxy_library.rb
# LibraryProxy is an abstract java class to give us access to the java
# reflection methods 'pre', 'post' and 'draw' (which we should implement).
# width, height, fill, stroke, background are available, otherwise use app.
class TestProxy < LibraryProxy # subclass LibraryProxy
  
  def pre; end # empty method is OK
     
  def draw
    fill 200
    app.ellipse(width / 2, height / 2, 20, 20)
  end
  
  def post; end # empty method is OK   
end
{% endhighlight %}

### Snapshot of the running sketch

Note the white circle in the red ellipse comes from the library class

<img src="/assets/library_proxy.png" />

[vanilla]:https://github.com/processing/processing/wiki/Library-Basics

