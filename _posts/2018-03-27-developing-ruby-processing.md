---
layout: post
title: "Developing JRubyArt"
date: 2018-03-27 06:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, processing, propane, developer, guide
---
### A Guide for anyone wishing to develop a ruby wrapper for processing

This article is intended as a guide/introduction for anyone who might want to join the ruby-processing group, to either maintain or develop [propane][propane] and/or [JRubyArt][jruby_art] (or both).

The first thing to understand is that vanilla processing is essentially a java project, hiding behind an ide and an antlr pre-processor. Historically it is interesting to understand that `ruby-processing` pre-dated both `processing.js` (since superseded by `p5*js`) and `processing.py`. [JRubyArt][jruby_art] and [propane][propane] require JRuby to run but in the case of `JRubyArt` jruby-complete.jar can be used instead of system installed version (similar to ruby-processing).

#### Knowledge base:-

A good working knowledge of your system environment is essential (this unfortunately disqualifies most Windows users and probably the majority of Mac users) you should at least be familiar with common system commands, or be prepared to learn.
Knowledge, experience of java programming is much more important than ruby, preferably familiarity with jruby (documentation is not all it should be so be prepared to learn as you go).

#### Recommended Tools:-

A means of swapping java environments (eg update-alternatives for Debian ubuntu)

A java ide, netbeans is a good choice

A ruby editor, atom is an excellent choice for git support, and ability to run sketches from the editor (further you can preview markdown).

Minitest is preferred over rspec for testing
An independent maven install (ie separate from ide that will also include maven support).

If at all possible avoid rvm, and probably rbenv they will only cause grief and confusion (neither handle jruby versioning too well)

#### Tricks of the trade

Run a sketch.pde in the processing ide, and somewhere it outputs the sketch.java and sketch.class artefacts in the case of linux this will be in folders in the `/tmp` directory. The sketch.java sketch can be transferred to your java ide and with right setup (added libraries) you should be able to run the sketch from the java ide. Do this and you are well on your way being able translate processing sketches to ruby, but mere translation should not be considered good enough it should look like ruby code.

Tools like [rubocop][rubocop] and [ruby-lint][lint] can be helpful, read Sandimetz [Practical Object-Orientated Design in Ruby][poodr] and/or watch her talks to be inspired to do the right thing.

#### Exercises

Clone the repositories, read the project code (propane is simpler to start with, neither code base is particularly big and I've tried to keep it modular), see if it makes sense, there could be scope for improvement.

##### Documentation

The simplest, safest place to contribute to is the documentation, start by contributing to Wiki, otherwise learn jekyll and github markdown.


[jruby_art]:https://ruby-processing.github.io/JRubyArt/
[propane]:https://ruby-processing.github.io/propane/
[rubocop]:http://rubocop.readthedocs.io/en/latest/
[lint]:http://code.yorickpeterse.com/ruby-lint/latest/
[poodr]:http://www.poodr.com/
