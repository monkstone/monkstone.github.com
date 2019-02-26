---
layout: post
title: "Refinements for JRubyArt"
date: 2019-1-14 06:00:00
categories: jruby_art update
keywords: jruby_art, Numeric, super, radians
---

Headius has now fixed to some extent refinements in JRuby, thus it should be possible to use refinements to provide `:radian` and `:degree` methods to Numeric, rather than monkey patching global space.
``` ruby
module SuperNumeric
  refine Numeric do
    def degrees
      self * 57.29577951308232
    end

    def radians
      self * 0.017453292519943295
    end
  end
end

using SuperNumeric

puts 45.radians
puts 0.7853981634.degrees
```
