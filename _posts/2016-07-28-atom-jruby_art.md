---
layout: post
title: "Setting up Atom for JRubyArt"
date: 2016-07-29 16:00:00
categories: jruby_art update
keywords: atom, processing, jruby_art 
permalink: _posts/atom-jruby_art
---

### Install Atom ###

See [website][atom] (or scroll to bottom of [releases page for downloads][releases] on github)

For linux ignore any distro version and download latest version (at least 1.8.0) and:-

```bash
sudo dpkg --install atom-amd64.deb # debian, mint, ubuntu
```

Mac/Windows users could just download direct (or homebrew on Mac)

Tough luck if you want the 32 bit version on linux

### Install Packages ###

From the atom editor install the `build`, `rake-build` and `script` packages.

To explore JRubyArt examples open `k9_samples` folder as a project, select `build` from the menu, then choose the rake tasks from the sub-menu, launches sketches in a random order per folder.

### What's it look like ###

<img src="/assets/rakefile.png" />

Emacs wrapped sketch files will run directly with the `script` package but future work (you could do this) may enable `k9 --run sketch` from the editor _cf_ [jEdit][jedit] and [vim][vim].

[atom]:https://atom.io/
[releases]:https://github.com/atom/atom/releases/tag/v1.8.0
[jedit]:http://ruby-processing.github.io/JRubyArt/editors
[vim]:http://ruby-processing.github.io/JRubyArt/editors