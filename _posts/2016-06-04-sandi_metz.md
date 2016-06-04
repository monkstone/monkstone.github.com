---
layout: post
title: "Subtractive Roman Numerals"
date: 2016-06-04 06:58:00
categories: jruby_art update
keywords: sandi metz, 99, JRubyArt, roman numerals
permalink: sandi
---

The latest Sandi Metz book (99 Bottles) is due soon why not join the [mailing list][list]. In her recent post, Sandi succesfully tackles the [Roman Numeral Kata][kata]. This was only possible because there are actually strict rules governing the legal subtractive form of roman numerals (back in the day they probably stuck to the additive forms anyway, _kept the stone masons busy_). Ninety-nine as in example below is a case in point where `IC` for example is illegal (preceding character letters cannot be less than times the value of the character they precede). See gist for [roman_numerals.rb][roman] or sign up to [Sandi's mailing list][list] for the original.

### blackboard.rb ###

{% highlight ruby %}

# blackboard.rb inspired by madam Sandi
require_relative 'roman_numerals'
attr_reader :f, :ninety_nine, :fifty_seven, :forty_two

ADDFORM = '%s plus %s equals %s'
SUBFORM = '%s minus %s equals %s'
using Roman

def setup
  sketch_title 'Math Blackboard'
  @f = createFont('Arial', 24, true)
  @ninety_nine = 'XCIX'
  @fifty_seven = 'LVII' 
  @forty_two = 'XLII'
end

def draw
  background 10
  text_font(f, 24)
  fill(0, 220, 220)
  text('Roman Math Blackboard JRubyArt', 80, 50)
  text(format(ADDFORM, ninety_nine, fifty_seven, add(ninety_nine, fifty_seven)), 80, 80)
  text(format(ADDFORM, ninety_nine, forty_two, add(ninety_nine, forty_two)), 80, 105)
  text(format(ADDFORM, forty_two, fifty_seven, add(forty_two, fifty_seven)), 80, 130)
  text(format(SUBFORM, ninety_nine, fifty_seven, sub(ninety_nine, fifty_seven)), 80, 155)
  text(format(SUBFORM, ninety_nine, forty_two, sub(ninety_nine, forty_two)), 80, 180)
end

def add(a, b)
  (a.to_i(:roman) + b.to_i(:roman)).to_roman
end

def sub(a, b)
  (a.to_i(:roman) - b.to_i(:roman)).to_roman
end

def settings
  size 690, 240
end

{% endhighlight %}

<img src="/assets/roman.png" />

[jruby_art]:https://ruby-processing.github.io/index.html
[roman]:https://gist.github.com/monkstone/b35b26384976c6e474ebf5a33d7abab7
[list]:http://sandimetz.us3.list-manage.com/track/click?u=1090565ccff48ac602d0a84b4&id=bd938590a4&e=8144e160c6
[kata]:https://github.com/alxndr/exercism/blob/master/ruby/roman-numerals/roman_numerals_test.rb