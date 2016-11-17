---
layout: post
title: "Toward Literate Programming"
date: 2016-11-17 04:00:00
categories: jruby_art update
keywords: java, kids, literate, ruby, smalltalk
---
Recently `processing` and `p5*js` have been promoted as ways to teach programming to kids. I have never thought either language to be particulary suitable for such a task despite the obvious enthusiasm (and almost infectious enthusiasm) of Lauren McCarthy and Dan Shiffman.  I wasn't even particulary interested in coding for kids apart from an experimental project by [Marcel Bellidin][Marcel Bellidin] which uses ruby-processing to create a simple live coding environment for kids, that was in part inspired by [Bret Victor][Bret Victor].  Now I could easily update [Marcel Bellidin][Marcel Bellidin] code to work with propane (or even JRubyArt), but I though I would follow the link to [Bret Victor][Bret Victor] to find what had inspired the original work. It was then that I found he is not at all impressed with `processing` as tool for teaching kids, indeed so much so he wrote a lengthy post critizing the [khan academy][khan academy] approach and the related link to his blog by [John Resig][John Resig].  

What drew me back to the [Marcel Bellidin][Marcel Bellidin] project was [SonicPi][SonicPi] which seems to tick most of the boxes when it comes to a kid friendly coding environment.  I think it is interesting that ruby was chosen for [SonicPi][SonicPi], that was aimed at kids in the first place (as I understood processing was originally designed with artists in mind?).  The [SonicPi][SonicPi] GUI is clean simple and feedback is approximately immediate.  I am sure we could do a better job in ruby (albeit using processing in the background). Here is an early idea, that would support the OO style of coding (ruby took some inspiration from Smalltalk, I seem to remember something to do with [frogs in the OU toolkit][OU]) PS: this is only an idea, but named parameters (ie for humans not machines, has to be a good thing?):-

Now let me first say I've not read [mindstorms][mindstorms] which is recommended reading (by Bret Victor), nor do I have inclination or time, but ruby is surely a better starting point to teach kids than a clipped version of java.

{% highlight ruby %}
#!/usr/bin/env jruby
require 'propane'

## code written by a programmer ########################################
## implementation should be hidden but interface documented
class Rectangle
  attr_reader :xpos, :ypos, :width, :height

  def initialize(xpos:, ypos:, width:, height:)
    @xpos, @ypos, @width, @height = xpos, ypos, width, height
  end

  def draw
    $app.fill 0, 0, 200 # color could be another parameter `fill state` is a concept too far
    $app.rect(xpos, ypos, width, height)
  end
end
########################################################################


class Simple < Propane::App

  ## code written by a programmer ########################################
  def clear_screen
    background 0
  end
  ########################################################################
  def settings
    size 600, 600
  end

  def setup
    sketch_title 'Simple'
  end

  def draw
    ## literate code
    clear_screen # explicitly clear screen
    fred = Rectangle.new(
      xpos: mouse_x,
      ypos: mouse_y,
      width: 40,
      height: 40
    )
    fred.draw # send message draw to fred
    ##
  end
end

Simple.new

{% endhighlight %}

<img src="/assets/live.png" />

[Marcel Bellidin]:https://github.com/marcel12bell/simple-live-coding/
[khan academy]:http://www.khanacademy.org/cs/
[John Resig]:http://ejohn.org/blog/introducing-khan-cs/
[Bret Victor]:http://worrydream.com/#!/LearnableProgramming/
[SonicPi]:http://sonic-pi.net/
[mindstorms]:https://books.google.co.uk/books?id=HhIEAgUfGHwC&printsec=frontcover&redir_esc=y&hl=en#v=onepage&q&f=false
[OU]:http://www.tutorials4u.com/smalltalk/smalltalk-tutorial-01.htm
