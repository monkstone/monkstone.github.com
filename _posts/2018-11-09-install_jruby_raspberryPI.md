---
layout: post
title: "Installing JRuby-9.2.2.0 on RaspberryPI"
date: 2018-11-09 06:00:00
categories: jruby_art update
keywords: jruby
---

Here is a dead simple way to install jruby on a RaspberryPI with (Raspbian Stretch OS).

Download my [bashscript](https://gist.github.com/monkstone/2b34b996d40b9eca255fadef5ee6d7dd) and run as follows:-

```bash
sudo bash install_jruby.sh
```
Or if you prefer perform each command manually.

Check install with:-

```bash
jruby --version
```

Or fire up an irb session using jruby

```bash
jirb
# or jruby -S jirb
```

It also makes sense to set up your gem environment so you don't need to use `sudo` to install gems. Conventionally gems are stored in users `~/.gem` folder, with subdirectory of version number (vanilla ruby with PI is at version 2.3.0)

```bash
mkdir -p ~/.gem/ruby/2.3.0
```

Now edit user `~/.profile` to define `GEM_HOME` and `PATH`

```bash
export GEM_HOME="$HOME/.gem/ruby/2.3.0"
export GEM_PATH="$HOME/.gem/ruby/2.3.0"
# set PATH so it includes user's private bin directories
PATH="$HOME/bin:$HOME/.local/bin:$GEM_HOME/bin:$PATH"
```

Now you can use `jgem` to install `jruby` gems eg `PiCrate` a standalone version of ruby-processing

```bash
jgem install picrate
```

To test the install:-

```bash
picrate -i Samples
```
To run `PiCrate` demo:
```bash
cd `picrate_samples`
rake
```
