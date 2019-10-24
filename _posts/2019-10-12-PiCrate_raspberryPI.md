---
layout: post
title: Picrate-1.2.0 Raspbian Stretch
date: 2019-10-12T06:00:00.000Z
categories: jruby_art update
keywords: 'jruby, picrate, processing, RaspberryPI, Oracle, java'
---

I have just released what will probably be the final version of PiCrate for rasbian stretch on RaspberryPI3+. It is specifically designed to use the Oracle jdk8 that comes with the stretch distribution, here is a snapshot of sketch running from the Geany IDE:-

![stretch.png]({{site.github.url}}/assets/stretch.png)

I have already created a [jdk11 branch][branch] to support Buster. Currently you need to install OpenJDK11 yourself:-

```bash
apt-get install openjdk-11-jdk
```

Because of this change, the next release of PiCrate will be version 2, currently OpenJDK11 is not as performant asthe Oracle java.

[branch]: https://github.com/ruby-processing/PiCrate/tree/jdk11
