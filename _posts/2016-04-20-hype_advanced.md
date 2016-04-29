---
layout: post
title: "Advanced Use of hype Library"
date: 2016-04-20 12:04:00
categories: jruby_art update
keywords: library, hype, JRubyArt
---

Here is another example sketch by Joshua Davis, that has been refactored for [JRubyArt][jruby_art]. Download the [hype][hype_library] library from [github][hype_library]. Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.0.2 ide. Note that we can use snake case in place of camel case, for constants use `::` and not `.` to call. The important thing to learn from this sketch is how to implement the HCallback interface. 

{% highlight java %}
package hype;

public interface HCallback {
    public void run(Object obj);
}

{% endhighlight %}

Which in vanilla processing Joshua Davis implements using an anonymous class:-

{% highlight java %}

.onCreate(
    new HCallback() {
    public void run(Object obj) {
      HDrawable d = (HDrawable) obj;
      d.noStroke().noFill().loc( (int)random(width), (int)random(height) ).visibility(false);
      swarm.addTarget(d);
    }
  }
  )
  
{% endhighlight %}

But since java 8 he could have used the java lambda form:-

{% highlight java %}

.onCreate((Object obj) -> {
                    HDrawable d = (HDrawable) obj;
                    d.noStroke().noFill().loc((int) random(width), (int) random(height)).visibility(false);
                    swarm.addTarget(d);
                })
                
{% endhighlight %}

In JRubyArt it is implemented as a closure (block), note we do not/should not try to use the vanilla processing method.

{% highlight ruby %}

.on_create do |obj|
  obj.no_stroke.no_fill.loc(rand(0..width), rand(0..width)).visibility(false)
  swarm.add_target(obj)
  
{% endhighlight %}  

Another important thing to note is this sketch requires the `--nojruby` flag to run;

{% highlight bash %}
k9 --nojruby run magnetic_field.rb
{% endhighlight %}

### magnetic_field.rb ###

{% highlight ruby %}
# encoding: utf-8
# frozen_string_literal: true
load_library :hype
include_package 'hype'
# Use Hype namespace
module Hype
  java_import 'hype.extended.layout.HGridLayout'
  java_import 'hype.extended.behavior.HMagneticField'
  java_import 'hype.extended.behavior.HSwarm'
end

attr_reader :pool, :pool_swarm, :field, :swarm
NUM_MAGNETS = 10

def settings
  size(640, 640)
end

def setup
  sketch_title 'Magnetic Field'
  H.init(self)
  H.background(color('#000000'))
  @field = Hype::HMagneticField.new
  NUM_MAGNETS.times do
    if rand > 0.5
      # x, y, north polarity / strength =  3 / repel
      field.add_pole(rand(0..width), rand(0..height), 3)
    else
      # x, y, south polarity / strength = -3 / attract
      field.add_pole(rand(0..width), rand(0..height), -3)
    end
  end

  @pool = HDrawablePool.new(2_500)
  pool.auto_add_to_stage
      .add(HShape.new('arrow.svg').enable_style(false).anchor_at(H::CENTER))
      .layout(Hype::HGridLayout.new.start_x(-60).start_y(-60).spacing(16, 16).cols(50))
      .on_create do |obj|
        obj.no_stroke.anchor(-20, -20)
        field.add_target(obj)
      end
      .requestAll

  @swarm = Hype::HSwarm.new.add_goal(width / 2, height / 2).speed(7).turn_ease(0.03).twitch(20)
  @pool_swarm = HDrawablePool.new(NUM_MAGNETS)
  pool_swarm.auto_add_to_stage
            .add(HRect.new(5))
            .on_create do |obj|
              obj.no_stroke.no_fill.loc(rand(0..width), rand(0..width)).visibility(false)
              swarm.add_target(obj)
            end
            .requestAll
end

def draw
  pool_swarm.each_with_index do |d, i|
    p = field.pole(i)
    p.x = d.x
    p.y = d.y
  end
  H.draw_stage
end

{% endhighlight %}

<img src="/assets/magnetic.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
