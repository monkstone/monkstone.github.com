---
layout: post
title: V3D Drivers on RaspberryPI
date: 2020-03-21T06:00:00.000Z
categories: jruby_art update
keywords: 'PiCrate, VC4, V3D, Mesa, glxinfo'
---
It is surprising that `mesa-utils` package does get installed by default with Raspbian Buster, since it provides the useful diagnostic tool `glxinfo`

```bash
sudo apt-get install mesa-utils
```
It is particularly useful in determining what OpenGl renderer is in use as follows:-
```bash
glxinfo|grep render
```

1. Raspbian Buster PI 3B+ Legacy renderer
```bash
direct rendering: Yes
OpenGL renderer string: llvmpipe (LLVM 9.0.1, 128 bits)
```
2. Raspbian Buster PI 3B+ FakeKMS renderer
```bash
direct rendering: Yes
OpenGL renderer string: VC4 V3D 2.1
```
3. Raspbian Buster PI 3B+ FullKMS renderer
```bash
direct rendering: Yes
OpenGL renderer string: VC4 V3D 2.1
```
4. Raspbian Buster PI 4 Legacy renderer
```bash
direct rendering: Yes
OpenGL renderer string: llvmpipe (LLVM 9.0.1, 128 bits)
```
5. Raspbian Buster PI 4 FakeKMS renderer
```bash
direct rendering: Yes
OpenGL renderer string: V3D 4.2
```
In my experience FakeKMS on the raspberryPI works just fine with processing P2D and P3D renderers. FullKMS on the RaspberryPI4 works fine with simpler P2D and P3D sketches. The Legacy drivers are unusable with P2D and P3D sketches.
