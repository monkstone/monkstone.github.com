---
layout: post
title: "The LibraryLoader Module in JRubyArt"
date: 2015-12-02 00:00:00
categories: jruby_art update
keywords: library, library loader, libraries, JRubyArt
permalink: /library_loader/
---

The JRubyArt [LibraryLoader module][library_loader] is essentially the same as that developed for ruby-processing. But since processing-3.0 there is the distinct possibility that you will need to specify the vanilla processing sketchbook folder (in `.jruby_art/config.yml`), for it to work properly. As a convenience you can call the module using either `load_library` or `load_libraries` from your sketch. The preferred way call the library / libraries is as a symbol, comma separated list of symbols _but Strings also work see below for core java libraries_:-

{% highlight ruby %}
load_library :sound
load_libraries :sound, :pdf
load_library 'sound'
load_libraries 'sound', 'pdf'
{% endhighlight %}

To use classes from the sound package say with full-qualified class name use:-

{% highlight ruby %}
generator = Java::ProcessingSound::WhiteNoise.new(self)
{% endhighlight %}

To avoid using the full-qualified class name use `include_package`

{% highlight ruby %}
include_package 'processing.sound'

generator = WhiteNoise.new(self)
{% endhighlight %}

The contributed processing libraries are called in the same way, as well as ruby libraries such `boids` and `control_panel` _the latter being a bit of a hybrid_.

{% highlight ruby %}
load_library :control_panel
load_library :boids
{% endhighlight %}

[library_loader]:https://github.com/ruby-processing/JRubyArt/blob/master/lib/jruby_art/library_loader.rb
