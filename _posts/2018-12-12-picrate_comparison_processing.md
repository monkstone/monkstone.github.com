---
layout: post
title: "PiCrate vs processing on RaspberryPI"
date: 2018-12-12 06:00:00
categories: jruby_art update
keywords: jruby, picrate, install, processing, RaspberryPI
---

PiCrate is a stand-alone ruby wrapper around vanilla processing, that does not depend on a vanilla processing install. It uses the java environment that is already installed on raspbian-stretch (much lighter weight than vanilla processing with its bundled jvm), however it does require an install of JRuby. A PiCrate sketch is pretty much just ruby code (there is no fancy pre-processor step _cf_ vanilla processing), and there are many editors (_eg_ vim) and ide (_eg_ geany) that support coding in ruby (_cf_ vanilla processing that requires its dedicated ide, that is java based, and hence memory intensive _grew out of JEdit_).

Vim is incredibly lightweight but mainly suitable for power users, whereas geany is more suitable for those requiring a GUI, but is still quite lightweight. Further you can launch sketches directly from both.
