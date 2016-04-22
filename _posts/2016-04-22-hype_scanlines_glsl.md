---
layout: post
title: "Using GLSL shaders with hype Library"
date: 2016-04-22 13:56:00
categories: jruby_art update
keywords: library, hype, JRubyArt
---

Here is an example sketch by Joshua Davis, from the [hype framework][hype_framework] that has been changed to make use of ruby syntax.
The declared aim of the [hype][hype_library] library is to provide:-
__A collection of classes that performs the heavy lifting so that you can create sketches with the minimum amount of code__. When using the [hype][hype_library] library to create [JRubyArt][jruby_art] sketches it is suggested that you not simply ape the [hype framework][hype_framework] examples, but tailor your sketches to take advantage of the ruby language to create even more elegant code (Code as Art), not that it is relevant here. 

Unzip the library in the processing libraries folder, rename the folder `hype`, rename `distribution` folder to `library`, rename the `HYPE.jar` to `hype.jar`. Check that you can see the library from the processing-3.0.2 ide. Note that we can use snake case in place of camel case, for constants use `::` and not `.` to call. The important thing to learn from this sketch is how to implement the HCallback interface. This can be implemented as a closure (block), note we do not/should not try and use the vanilla processing method. But when we need to cast the object to a `HDrawable` type, we do this using `to_java(Java::Hype::HDrawable)` function.

In this case we use ruby syntax in the creation of web colors (as color int).

### h_canvas.rb ###

{% highlight ruby %}
# encoding: utf-8
# frozen_string_literal: true
# Using a simplified scanline shader, this example demonstrates how to apply
# a PShader to an HCanvas object.
#
# In the two swarms on screen, half of them have scanlines. These are the
# drawables that are children of the canvas. The scanline shader is directly
# applied to the HCanvas and does not affect the drawables on the stage.
#
# Set the HCanvas renderer to P2D or P3D when using HShaders
load_library :hype
include_package 'hype'
# Access through Hype namespace
module Hype
  java_import 'hype.extended.colorist.HColorPool'
  java_import 'hype.extended.behavior.HTimer'
  java_import 'hype.extended.behavior.HSwarm'
end

attr_reader :my_shader, :swarm, :timer

COLS = %w(#FFFFFF #F7F7F7 #ECECEC #333333 #0095a8 #00616f #FF3300 #FF6600).freeze

def settings
  size(640, 640, P3D)
end

def setup
  sketch_title 'Scanlines GLSL sketch'
  H.init(self)
  H.background(color('#000000'))
  cols = COLS.map { |col| color(col) }
  @my_shader = load_shader('scanlines.glsl')
  my_shader.set('resolution', 1.0, 1.0)
  my_shader.set('screenres', width.to_f, height.to_f)
  my_shader.set('time', millis / 1000.0)
  colors = Hype::HColorPool.new(*cols)
  canvas_shader = H.add(HCanvas.new(P3D)
                   .auto_clear(true)
                   .shader(my_shader))
                   .to_java(Java::Hype::HCanvas)
  swarm = Hype::HSwarm.new.add_goal(H.mouse).speed(5).turn_ease(0.05).twitch(20)
  pool1 = HDrawablePool.new(20)
  pool1.auto_add_to_stage
       .add(HRect.new.rounding(4))
       .on_create do |obj|
    d = obj.to_java(Java::Hype::HDrawable)
    d.size(rand(50..100))
     .fill(colors.get_color)
     .no_stroke
     .loc(width / 2, height / 2)
     .anchor_at(H::CENTER)
    swarm.add_target(d)
  end
  pool2 = HDrawablePool.new(20)
  pool2.auto_parent(canvas_shader)
       .add(HRect.new.rounding(4))
       .on_create do |obj|
    d = obj.to_java(Java::Hype::HDrawable)
    d.size(rand(50..100))
     .fill(colors.get_color)
     .no_stroke
     .loc(width / 2, height / 2)
     .anchor_at(H::CENTER)
    swarm.add_target(d)
  end
  @timer = Hype::HTimer.new
                       .num_cycles(pool1.num_active)
                       .interval(250)
                       .callback do
    pool1.request
    pool2.request
  end
end

def draw
  H.draw_stage
  my_shader.set('time', millis / 1000.0)
end

{% endhighlight %}

scanlines.glsl
{% highlight glsl %}
/*
	A basic scanline shader for Processing, based on MattiasCRT: https://www.shadertoy.com/view/Ms23DR
*/

#define PROCESSING_TEXTURE_SHADER

#ifdef GL_ES
precision mediump float;
#endif

uniform sampler2D texture;
varying vec4 vertTexCoord;
uniform vec2 resolution;
uniform vec2 screenres;
uniform float time;

void main(void) {

	vec3 iResolution = vec3(resolution,0.0);
	float iGlobalTime = time;

	vec2 q = vertTexCoord.xy / iResolution.xy;
	vec2 uv = q;
	vec4 col = texture2D(texture, vertTexCoord.xy);

	float scans = clamp( 0.35+0.35*sin(3.5 * iGlobalTime + uv.y * screenres.y * 2.0), 0.0, 1.0);
	float s = pow(scans,1.7);
	col = col * vec4(0.4 + 0.7 * s) ;

	col*=1.0-0.65*vec4(clamp((mod(vertTexCoord.x, 2.0)-1.0)*2.0,0.0,1.0));

	gl_FragColor = col;
}
{% endhighlight %}

<img src="/assets/scanlines.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/
