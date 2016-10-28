---
layout: post
title: "Atom editor as a propane ide"
date: 2016-10-28 10:00:00
categories: jruby_art update
keywords: atom, processing, propane, markdown, ruby

---
### Code snippets ###

Install the `language-jruby-art` package to create outline sketches, and/or add sketch snippets.

### Run Sketches ###

Use the `script` package to run propane sketches from the atom editor.

### Add Extra Documentation for Sketches in Markdown ###

You can add documentation for propane using the atom editor as follows

```markdown

### Simplex Noise ###

Mainly inspired by Karsten Schmidt and Stefan Gustavson I created a `simplex_noise` library for propane (mainly in java) but I tweeked the example to work in ruby. Read more from [Stefan Gustavson][simplex] who created the java code. Kudos to Karsten Schmidt for the test code (vanilla processing not ruby version)

[simplex]:http://webstaff.itn.liu.se/~stegu/simplexnoise/simplexnoise.pdf

```
### Preview mardown documents ###

Use the build in markdown preview capabilities (_of atom_) to preview your documentation see below.


__Bringing it all together:-__

See `SimplexNoise.md` preview in right panel (with running sketch on top) and the sketch code in the left hand panel.

![atom.png]({{ github.site.url }}/assets/atom.png)
