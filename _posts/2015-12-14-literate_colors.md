---
layout: post
title:  "Literate Colors in JRubyArt"
date:   2015-12-14 00:05:13
categories: jruby_art update
---

If you are coming to JRubyArt from vanilla processing, you may not be aware of quite how elegant ruby can be:-

{% highlight ruby %}
web = %w(#841B2D #c0c0c0 #ffd700)
names = %i(RUBY SILVER GOLD)
colours = names.zip(web).to_h
background color(colours[:RUBY])
{% endhighlight %}

In the first three lines we have created a (hash) dictionary of "web colors" that we can use in our sketch. You can look up web colors for yourself to create your own color palette.

In the first line we create and array of string using the %w() shortcut.

In the second line we create and array of symbols using the %i() shortcut.

In the third line we zip the two arrays, and convert them to a hash dictionary.

Through the magic of JRubyArt the above code is a valid ruby sketch which you might like to explore with the `k9 watch` command as shown below, where we have opened up a vim editor on the file and edited the color to `:GOLD` from `:RUBY`, and the sketch has reloaded from save (ie no need to close editor).
Other editors are available...

<img src="/assets/watch.png" />

