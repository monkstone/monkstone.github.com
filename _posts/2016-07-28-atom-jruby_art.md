---
layout: post
title: "Setting up Atom for JRubyArt"
date: 2016-07-29 16:00:00
categories: jruby_art update
keywords: atom, processing, jruby_art 
permalink: /atom-jruby_art/
---

### Install Atom ###

See [website][atom] (or scroll to bottom of [releases page for downloads][releases] on github)

For linux ignore any distro version and download latest version (at least 1.9.0) and:-

```bash
sudo dpkg --install atom-amd64.deb # debian, mint, ubuntu
```

Mac/Windows users could just download direct (or homebrew on Mac)

Tough luck if you want the 32 bit version on linux

### Install Packages ###

From the atom editor install the `project-runner` and `atom-k9` packages.

To explore JRubyArt examples open `k9_samples` folder, select `project-runner` from the menu, then choose the run from the sub-menu, launches sketches in a random order per folder.

### What's it look like ###

<img src="/assets/rakefile.png" />

Using the [atom-k9][atom-k9] package you can run even bare sketches from the atom editor just like [jEdit][jedit] and [vim][vim].

[atom]:https://atom.io/
[atom-k9]:https://atom.io/packages/atom-k9
[releases]:https://github.com/atom/atom/releases/tag/v1.9.0
[jedit]:https://ruby-processing.github.io/JRubyArt/editors
[vim]:https://ruby-processing.github.io/JRubyArt/editors