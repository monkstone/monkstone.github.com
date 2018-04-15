---
layout: post
title: "Fixing propane for jdk9"
date: 2018-04-13 06:00:00
categories: jruby_art update
permalink: /jdk9/
keywords: ruby-processing, JRubyArt, processing, propane, developer, jdk9
---
### The problems

Changes in jdk9 have affected the ability to call inherited protected methods with jruby. For regular java classes, there is an expectation that inheriting java classes can call a protected method of the parent. But at the moment headius (lead jruby developer) is reluctant to create a jruby fix as this would probably expose other methods that would not normally available. The most serious problem is that we face is that we use a protected method `run_sketch` to initiate sketches in [JRubyArt][jruby_art] and [propane][propane], but there is also an issue with the `control_panel` library, where we call the protected method `fireStateChanged` on our `Slider` class, that inherits from swing `JSlider`.

Other changes affect the ability to use proprietary code, currently this most affects FX2D and FX3D sketches, but may also affect [processing.org][vanilla] hacks on MacOS, these things might be fixed by processing developers but not until September 2018 at the earliest.

#### Fixing run_sketch

A bit radical but it works we create a public `runPropane()` method for `PApplet` that mirrors `runSketch()`, except that it is publicly accessible.

wget https://github.com/processing/processing/archive/processing-0264-3.3.7.tar.gz

tar xzvf processing-0264-3.3.7.tar.gz

See github project [processing-core][core] for the modification to `PApplet` and polyglot maven build. All that is the required is to modify propane `app.rb` to call `runPropane` instead of `run_sketch`. However not 100% sure fix preserves processing MacOS hacks, we do however take the opportunity to use our own icons.

#### Fixing control_panel

We can avoid calling the `fireStateChanged` if we initialize the slider values directly in the sketch. This is less neat but workable, first we create a method.

```ruby
module ControlPanel
  def self.app_value(name, val)
    Propane.app.instance_variable_set("@#{name}", val)
  end
  ...

```

so we can call it initially as well as in our change_listener

```ruby
add_change_listener do
  update_label(label, name, value)
  ControlPanel.app_value(name, value)
  proc.call(value) if proc
end
val = initial.nil? ? (range.first + range.last) * 50 : initial * 100
set_value(val)
ControlPanel.app_value(name, val)

```

previously it was sufficient to `fireStateChanged` and let the change_listener initialize the sketch values. If the val is new that fires state changed for us, otherwise `label` only shows after slider is moved.


[vanilla]:https://github.com/processing/processing/wiki/Supported-Platforms#java-9
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
[propane]:https://ruby-processing.github.io/propane/
[core]:https://github.com/ruby-processing/processing-core
