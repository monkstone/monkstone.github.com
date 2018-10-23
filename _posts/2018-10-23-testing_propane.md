---
layout: post
title: "Testing propane with JDK-11"
date: 2018-10-23 06:00:00
categories: jruby_art update
keywords: propane, pre, java-11
---

* Install [java-11][oracle] (probably from Oracle)

* Install [jruby-9.2.0.0][jruby]

* Installing `propane-2.5.0.pre-java`
```bash
jgem install --pre propane
# alternative
jruby -S gem install --pre propane
```

### Testing

1. propane --version
2. propane -c test_propane 400 200
3. jruby test_propane.rb _sketch should run_

[propane]:https://github.com/ruby-processing/propane/tree/jdk11


[oracle]:https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html

[jruby]:https://www.jruby.org/download
