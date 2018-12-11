---
layout: post
title: "Installing picrate and JRuby-9.2.5.0 on RaspberryPI"
date: 2018-11-09 06:00:00
categories: jruby_art update
keywords: jruby, picrate, install, processing, RaspberryPI
---

Here is easy way to do a virgin install of `picrate` and `jruby` on a RaspberryPI with (Raspbian Stretch OS).

Download my [Rakefile](https://github.com/monkstone/install-picrate/releases) and run as follows:-

```bash
rake # to run default task of Rakefile
```
To list rake Tasks:-

```bash
rake --tasks
```
NB: you will be prompted for password as some tasks require `sudo` authority (but you should __not__ run `rake` with `sudo` authority)

Once everything is installed you use `geanyIDE` to explore example sketches (after loading `PiCrate` project) or to run demo from console:-

To run `PiCrate` demo:
```bash
cd `~/picrate_samples`
rake
```
