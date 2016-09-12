---
layout: post
title: "Setting up Atom for Processing on Linux"
date: 2016-07-28 13:00:00
categories: jruby_art update
keywords: atom, processing, linux, debian 
permalink: /atom-debian/
---

### Install Atom (debian/mint/ubuntu)###

Probably ignore distro version download latest version (at least 1.8.0) and:-

```bash
sudo dpkg --install atom-amd64.deb
```

Tough luck if you want the 32 bit version

You must provide a link to the `processing-java` script, I would use:-

```
sudo update-alternatives --install /usr/local/bin/processing-java processing-java /home/tux/processing-3.2.1/processing-java 50
```


### Install Packages ###

From the atom editor install the `processing` and `processing-language` packages.

What`s it look like?:-

`ctrl+alt+b` to run the sketch (as with vanilla processing sketch must live in an eponymous folder nuts or what?)

<img src="/assets/processing_atom.png" />

### For the adventurous ###

You could write your own package to `k9 --run` JRubyArt sketches, `emacs` wrapped sketches can run as ruby scripts, so only need the `script` package to run them.

PS: there is probably bit of performance hit cf running from jEdit or the processing ide (but the look and feel is much better). With JRubyArt you could create an outline sketch from a regular console ideally in empty folder:-

```bash
k9 --create fred 200 200
```

Set it running in watch mode

```
k9 --watch fred.rb
```
Then edit the sketch from atom, to see the effect of your changes you only need to save the sketch (best of all worlds, a clean modern editor that should not compromise performance). 
