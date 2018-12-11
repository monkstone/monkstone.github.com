---
layout: post
title: "Installing picrate and JRuby-9.2.5.0 on RaspberryPI"
date: 2018-11-09 06:00:00
categories: jruby_art update
keywords: jruby, picrate, install, processing, RaspberryPI
---

Here is an easy way to do a virgin install of `picrate` and `jruby` on a RaspberryPI with (Raspbian Stretch OS).

Download my [Rakefile](https://github.com/monkstone/install-picrate/releases) and run as follows:-

```bash
rake # to run default task of Rakefile
```
To list rake Tasks:-

```bash
rake --tasks
```
NB: you will be prompted for password as some tasks require `sudo` authority (but you should __not__ run `rake` with `sudo` authority)

Once everything is installed you can use `geanyIDE` to explore example sketches (after loading `PiCrate` project) or to run demo from console:-


```bash
cd `~/picrate_samples`
rake
```

For opengl sketches (P2D, P3D) use vc4 graphics driver

```bash
dmesg | grep drm
[    4.856264] vc4-drm soc:gpu: bound 3f600000.firmwarekms (ops vc4_fkms_ops [vc4])
[    4.897489] vc4-drm soc:gpu: bound 3fc00000.v3d (ops vc4_v3d_ops [vc4])
[    4.902987] [drm] Initialized vc4 0.0.0 20140616 for soc:gpu on minor 0
[    4.903007] [drm] Supports vblank timestamp caching Rev 2 (21.10.2013).
[    4.903015] [drm] Driver supports precise vblank timestamp query.
[    5.024078] vc4-drm soc:gpu: fb0:  frame buffer device
```

For video capture use the legacy driver.
