---
layout: post
title: "py5 feature envy"
date: 2021-07-17 06:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, processing, py5
---
Recently I have been experimenting with [py5][py5] (an implementation of processing that can be run with native python-3.8+), previously there was [processing.py][processing.py] (based on [jython][jython], an implementation of python in java). In some ways ruby-processing projects are similar to processing.py except because of [jruby][jruby] they are somewhat closer to the the current native ruby than is jython that is currently stuck on python-2.7. The big frustration with processing.py and jython is the inability to use [numpy][numpy] and any other C library. This limitation similarly applies to my ruby-processing projects which are based on jruby, however thanks to its design it it is rather well integrated with java.

#### How does py5 manage to interoperate with native python?
It does this by using [PyPE][PyPE].
>JPype is a Python module to provide full access to Java from within Python. It allows Python to make use of Java only libraries, exploring and visualization of Java structures, development and testing of Java libraries, scientific computing, and much more. By gaining the best of both worlds using Python for rapid prototyping and Java for strong typed production code, JPype provides a powerful environment for engineering and code development.

>This is achieved not through re-implementing Python, as Jython has done, but rather through interfacing at the native level in both virtual machines. This shared memory based approach achieves decent computing performance, while providing the access to the entirety of CPython and Java libraries.

#### Using numpy with py5

See my [experiments with py5][expts], where I found using numpy did indeed confer great performance benefits to sketches involving pixel manipulation.

#### Unique features to py5

Ability to integrate with [jupyter notebooks][jupyter]


[jupyter]:https://jupyter.org/
[expts]:https://github.com/monkstone/py5-examples
[numpy]:https://numpy.org/
[jruby]:https://www.jruby.org/
[jython]:https://www.jython.org/
[processing.py]:https://github.com/jdf/processing.py#python-mode-for-processing
[py5]:http://py5.ixora.io/
[PyPE]:https://github.com/jpype-project/jpype/
