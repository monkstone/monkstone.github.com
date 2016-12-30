---
layout: post
title: "Setting up Atom for propane"
date: 2016-08-11 10:00:00
categories: jruby_art update
keywords: atom, processing, propane
permalink: /atom-propane/
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

From the atom editor install the `script` package.


See [Atom-Script][script] (but note we replace jruby as the ruby executable, we can do this by including shebang argument at the beginning of sketch)

### Running Sketches ###

To run sketches with a `data` folder you should initially open the immediate folder:

To explore propane samples open `~/propane_samples` folder and explore `processing_app`, select a file to view then `alt r` (windows/linux) or `ctrl r` mac to run the selected file.

### What's it look like ###

<img src="/assets/atom-runner.png" />

[script]:https://atom.io/packages/script
[atom]:https://atom.io/
[releases]:https://github.com/atom/atom/releases/tag/v1.9.0
[jedit]:https://ruby-processing.github.io/JRubyArt/editors
[vim]:https://ruby-processing.github.io/JRubyArt/editors
