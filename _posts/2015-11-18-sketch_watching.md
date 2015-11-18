---
layout: post
title:  "Sketch Watching"
date:   2015-11-18 06:45:00
categories: jruby_art update
---

### Automatically Reload your Sketch with `k9 watch`

`k9 watch` will keep an eye on the source files (*.rb and *.glsl only) of your sketch. Whenever you save a change to it, it'll reload your running code, so you can try out your ideas quickly. `k9 watch` is the best way to quickly prototype a sketch. To guard against you running watch in a top level folder we limit the number of files to `watch` to 20, but you can set a different limit in ~/.jruby_art/config.yml `MAX_WATCH: 100`. However the safest thing to do is make sure that you use watch from a custom folder to avoid `watching` non-sketch code. See Joanne Cheng video for a demo of the [ruby-processing watch mode][video].  See [Wiki][wiki] for true live mode with pry.

__TIP__

If you declare constants in your sketch you may wish to define them this way `CONSTANT ||= 'some value'` to avoid getting the `warning: already initialized constant` when the sketch re-loads.



[video]:https://vimeo.com/88994050
[wiki]:https://github.com/ruby-processing/JRubyArt/wiki/Live-Coding