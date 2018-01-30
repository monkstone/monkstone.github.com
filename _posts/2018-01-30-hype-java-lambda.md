---
layout: post
title: "Comparing using java lambda in a hype sketch to JRubyArt block"
date: 2018-01-30 07:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, hype, java, lambda
---
Since jdk8 it is possible to replace anonymous classes in callbacks with lambda (but you can't do this in processing ide). Here is [hype][:https://github.com/hype/HYPE_Processing] sketch converted to use java lambda:-

```java
import processing.core.PApplet;
import hype.H;
import hype.HCanvas;
import hype.HDrawable;
import hype.HDrawablePool;
import hype.HRect;
import hype.extended.behavior.HOscillator;
import hype.extended.behavior.HTimer;
import hype.extended.colorist.HPixelColorist;

public class HOscillatorSketch extends PApplet {

    HDrawablePool pool;
    HPixelColorist colors;
    HCanvas canvas;

    @Override
    public void setup() {
        H.init(this);
        H.background(0xff000000);
        H.use3D(true);
        colors = new HPixelColorist("gradient.jpg");
        canvas = new HCanvas(P3D).autoClear(true);
        H.add(canvas);
        pool = new HDrawablePool(1000);
        pool.autoParent(canvas)
                .add(new HRect().rounding(10))
                .onCreate((Object obj) -> {
                    int i = pool.currentIndex();

                    HDrawable d = (HDrawable) obj;
                    d
                            .noStroke()
                            .size((int) random(40, 80), (int) random(60, 80))
                            .loc((int) random(width), (int) random(height))
                            .anchorAt(H.CENTER)
                            .obj("xo", new HOscillator()
                                    .target(d)
                                    .property(H.X)
                                    .relativeVal(d.x())
                                    .range(-(int) random(5, 10), (int) random(5, 10))
                                    .speed(random(.005f, .2f))
                                    .freq(10)
                                    .currentStep(i)
                            )
                            .obj("ao", new HOscillator()
                                    .target(d)
                                    .property(H.ALPHA)
                                    .range(50, 255)
                                    .speed(random(.3f, .9f))
                                    .freq(5)
                                    .currentStep(i)
                            )
                            .obj("wo", new HOscillator()
                                    .target(d)
                                    .property(H.WIDTH)
                                    .range(-d.width(), d.width())
                                    .speed(random(.05f, .2f))
                                    .freq(10)
                                    .currentStep(i)
                            )
                            .obj("ro", new HOscillator()
                                    .target(d)
                                    .property(H.ROTATION)
                                    .range(-180, 180)
                                    .speed(random(.005f, .05f))
                                    .freq(10)
                                    .currentStep(i)
                            )
                            .obj("zo", new HOscillator()
                                    .target(d)
                                    .property(H.Z)
                                    .range(-400, 400)
                                    .speed(random(.005f, .01f))
                                    .freq(15)
                                    .currentStep(i * 5)
                            );
        })
                .onRequest((Object obj) -> {
                    HDrawable d = (HDrawable) obj;
                    d.scale(1).alpha(0).loc((int) random(width), (int) random(height), -(int) random(200));

                    HOscillator xo = (HOscillator) d.obj("xo");
                    xo.register();
                    HOscillator ao = (HOscillator) d.obj("ao");
                    ao.register();
                    HOscillator wo = (HOscillator) d.obj("wo");
                    wo.register();
                    HOscillator ro = (HOscillator) d.obj("ro");
                    ro.register();
                    HOscillator zo = (HOscillator) d.obj("zo");
                    zo.register();
        })
                .onRelease((Object obj) -> {
                    HDrawable d = (HDrawable) obj;

                    HOscillator xo = (HOscillator) d.obj("xo");
                    xo.unregister();
                    HOscillator ao = (HOscillator) d.obj("ao");
                    ao.unregister();
                    HOscillator wo = (HOscillator) d.obj("wo");
                    wo.unregister();
                    HOscillator ro = (HOscillator) d.obj("ro");
                    ro.unregister();
                    HOscillator zo = (HOscillator) d.obj("zo");
                    zo.unregister();
        });
        new HTimer(50)
                .callback((Object obj) -> {
                    pool.request();
        });
    }

    @Override
    public void draw() {
        for (HDrawable d : pool) {
            d.loc(d.x(), d.y() - random(0.25f, 1), d.z());

            d.noStroke();
            d.fill(colors.getColor(d.x(), d.y()));

            if (d.z() > -10 && d.z() < 10) {
                d.fill(0xffFFFFCC); // if the z axis hits this range, change fill to light yellow
            }
            if (d.y() < -40) {
                pool.release(d);
            }
        }
        H.drawStage();
    }

    @Override
    public void settings() {
        size(640, 640, P3D);
    }

    static public void main(String[] passedArgs) {
        String[] appletArgs = new String[]{"HOscillatorSketch"};
        if (passedArgs != null) {
            PApplet.main(concat(appletArgs, passedArgs));
        } else {
            PApplet.main(appletArgs);
        }
    }
}


```
But is even neater as a [JRubyArt][jruby_art] sketch where we can replace callbacks with a block

```ruby
load_library :hype

HYPE = %w[H HCanvas HDrawablePool HRect].freeze
EXTENDED = %w[
  behavior.HTimer behavior.HOscillator colorist.HPixelColorist
].freeze
hype_format = 'hype.%s'
hype_extended_format = 'hype.extended.%s'
HYPE.each { |klass| java_import format(hype_format, klass) }
EXTENDED.each { |klass| java_import format(hype_extended_format, klass) }

attr_reader :colors, :pool, :xo, :ao, :wo, :ro, :zo

def setup
  sketch_title 'Oscillator'
  H.init(self)
  H.background(color(0xff000000))
  H.use3D(true)
  @colors = HPixelColorist.new(data_path('gradient.jpg'))
  canvas = HCanvas.new(P3D).autoClear(true)
  H.add(canvas)
  @pool = HDrawablePool.new(1_000)
  pool.autoParent(canvas)
  .add(HRect.new.rounding(10))
  .on_create do |obj|
    i = pool.current_index
    obj.no_stroke
    .size(rand(40..80), rand(60..80))
    .loc(rand(width), rand(height))
    .anchorAt(H::CENTER)
    @xo = HOscillator.new
    .target(obj)
    .property(H::X)
    .relative_val(obj.x)
    .range(rand(-10..-5), rand(5..10))
    .speed(rand(0.005..0.2))
    .freq(10)
    .current_step(i)
    @ao = HOscillator.new
    .target(obj)
    .property(H::ALPHA)
    .range(50, 255)
    .speed(rand(0.3..0.9))
    .freq(5)
    .current_step(i)
    @wo = HOscillator.new
    .target(obj)
    .property(H::WIDTH)
    .range(-obj.width, obj.width)
    .speed(rand(0.05..0.2))
    .freq(10)
    .current_step(i)
    @ro = HOscillator.new
    .target(obj)
    .property(H::ROTATION)
    .range(-180, 180)
    .speed(rand(0.005..0.05))
    .freq(10)
    .current_step(i)
    @zo = HOscillator.new
    .target(obj)
    .property(H::Z)
    .range(-400, 400)
    .speed(rand(0.005..0.01))
    .freq(15)
    .current_step(i * 5)
  end
  .onRequest do |obj|
    obj.scale(1).alpha(0).loc(rand(width), rand(height), -rand(200))
    xo.register
    ao.register
    wo.register
    ro.register
    zo.register
  end.onRelease do
    xo.unregister
    ao.unregister
    wo.unregister
    ro.unregister
    zo.unregister
  end
  HTimer.new(50)
  .callback do
    pool.request
  end
end

def draw
  pool.each do |d|
    d.loc(d.x, d.y - rand(0.25..1), d.z)
    d.no_stroke
    d.fill(colors.get_color(d.x, d.y))
    # if the z axis hits this range, change fill to light yellow
    d.fill(color(0xffFFFFCC)) if d.z > -10 && d.z < 10
    pool.release(d) if d.y < -40
  end
  H.draw_stage
end

def settings
  size 640, 640, P3D
end

```
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
[hype_library]:https://github.com/hype/HYPE_Processing
[hype_framework]:http://www.hypeframework.org/
