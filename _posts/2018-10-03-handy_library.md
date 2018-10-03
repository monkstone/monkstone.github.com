---
layout: post
title: "The Handy library JRubyArt"
date: 2018-10-03 06:00:00
categories: jruby_art update
keywords: JRubyArt, java_alias, tap, sound, hand drawn, handy
---
The handy library is not available from processing ide, you will probably need to build it. Here is the `pom.xml` I used for a maven build, you only need the `gicentre.utils` and `jogl` to build the tests:-

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.gicentre.handy</groupId>
    <artifactId>Handy</artifactId>
    <version>2.0</version>
    <name>Handy</name>
    <properties>
        <polyglot.dump.pom>pom.xml</polyglot.dump.pom>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <jruby.api>http://jruby.org/apidocs/</jruby.api>
        <maven.compiler.target>1.8</maven.compiler.target>
        <jogl.version>2.3.2</jogl.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.processing</groupId>
            <artifactId>core</artifactId>
            <version>3.3.7</version>
            <type>jar</type>
        </dependency>
        <dependency>
            <groupId>org.jogamp.jogl</groupId>
            <artifactId>jogl-all</artifactId>
            <version>${jogl.version}</version>
        </dependency>
        <dependency>
            <groupId>org.jogamp.gluegen</groupId>
            <artifactId>gluegen-rt-main</artifactId>
            <version>${jogl.version}</version>
        </dependency>
        <dependency>
            <groupId>org.gicentre.utils</groupId>
            <artifactId>gicentreUtils</artifactId>
            <version>3.4</version>
            <type>jar</type>
        </dependency>        
    </dependencies>
</project>
```
For the following sketch I used the `handy.jar` from a local library folder:-
```ruby
load_library :handy
java_import 'org.gicentre.handy.HandyRenderer'
BALL_NUM = 6
BALL_COLORS = [[255, 0, 0], [255, 255, 0], [64, 64, 255]].freeze

attr_reader :handy, :balls

def settings
  size(400, 400)
end

def setup
  sketch_title 'Handy Ball Collision'
  HandyRenderer.__persistent__ = true # supress singleton warning
  @handy = HandyRenderer.new(self)
  @balls = (0...BALL_NUM).map { |idx| Ball.new(self, idx) }
end

def draw
  background(234, 215, 182)
  fill(0, 255, 0)
  handy.instance_eval do
    rect(20, 20, 360, 20)
    rect(20, 360, 360, 20)
    rect(20, 40, 20, 320)
    rect(360, 40, 20, 320)
  end
  balls.each(&:draw)
end

# Bouncing ball
class Ball
  attr_reader :sketch, :position, :delta, :minimum, :maximum

  def initialize(sketch, id)
    @sketch = sketch
    @position = Vec2D.new rand(100..300), rand(100..300)
    @delta = Vec2D.new rand(-6..6), rand(-6..6)
    @size = rand(60..100)
    @radius = @size / 2.0
    @color = BALL_COLORS[id % BALL_COLORS.size]
    @minimum = 40 + @radius
    @maximum = 360 - @radius
  end

  def draw
    @position += delta
    if position.x < minimum
      position.x = minimum
      delta.x *= -1
    end
    if position.x > maximum
      position.x = maximum
      delta.x *= -1
    end
    if position.y < minimum
      position.y = minimum
      delta.y *= -1
    end
    if position.y > maximum
      position.y = maximum
      delta.y *= -0.99
    end
    sketch.fill(*@color)
    sketch.handy.ellipse(position.x, position.y, @size, @size)
  end
end
```

![handy.gif]({{site.github.url}}/assets/handy.gif "Animated Gif")
