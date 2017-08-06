---
layout: post
title: "Extending LibraryProxy in JRubyArt"
date: 2017-08-04 06:00:00
categories: jruby_art update
keywords: processing, reflection, proxy, library, JRubyArt
---

### Vanilla processing Library Methods

Java processing provides the following __Library Methods__

1. pre()

   A Method that's called just after beginDraw(), meaning that it can affect drawing.
2. draw()

   Method that's called at the end of draw(), but before endDraw().
3. post()

   Method called after draw has completed and the frame is done. No drawing allowed.
4. mouseEvent(MouseEvent e)

   Called when a mouse event occurs in the parent applet. Drawing is allowed because mouse events are queued, unless the sketch has called noLoop().
5. keyEvent(KeyEvent e)

   Called when a key event occurs in the parent applet. Drawing is allowed because key events are queued, unless the sketch has called noLoop().

### LibraryProxy.java in JRubyArt

Since jruby_art-1.4.0 we can readily access all the above library methods in library classes and include them in our sketches (and TouchEvent could be added if required). For simplicities sake you should use the jruby_art `library_loader` to load the `proxy_library` into the sketch. We can then create ruby classes that inherit from `LibraryProxy` to give us access to the above methods. Note `LibraryProxy` is an abstract java class, because it's `draw` method is abstract. We need to implement that method in our ruby class, but an empty `draw` method will work just fine.

```java
package monkstone.core;

import processing.core.PApplet;
import static processing.core.PConstants.*;
import processing.event.MouseEvent;
import processing.event.KeyEvent;

/**
* The purpose of this class is to enable
* access to processing pre, draw and post loops in
* ruby-processing as a regular java library class.
* Also included background, fill and stroke methods.
* PConstants should also be available from static import
* @author Martin Prout
*/
public abstract class LibraryProxy {

  private final PApplet app;

  /**
  * Useful accessors
  */
  public int width, height;

  /**
  *
  * @param app PApplet
  */
  public LibraryProxy(PApplet app) {
    this.app = app;
    this.width = app.width;
    this.height = app.height;
    setActive(true);
  }

  /**
  * Extending classes can override this, gives access to, by reflection,
  * processing PApplet pre loop (called before draw)
  */
  public void pre(){}

  /**
  * Extending classes must implement this gives access to processing PApplet
  * draw loop
  */
  public abstract void draw();

  /**
  * Extending classes can override this, gives access to, by reflection,
  * processing PApplet post loop (called after draw)
  */
  public void post(){}

  /**
  * Extending classes can override this, gives access to, by reflection,
  * processing PApplet keyEvent method
  */
  public void keyEvent(KeyEvent e){}

  /**
  * Extending classes can override this, gives access to, by reflection,
  * processing PApplet mouseEvent method
  */
  public void mouseEvent(MouseEvent e){}

  /**
  * Register or unregister reflection methods
  * @param active
  */
  final void setActive(boolean active) {
    if (active) {
      this.app.registerMethod("pre", this);
      this.app.registerMethod("draw", this);
      this.app.registerMethod("post", this);
      this.app.registerMethod("mouseEvent", this);
      this.app.registerMethod("keyEvent", this);
      this.app.registerMethod("dispose", this);
    } else {
      this.app.unregisterMethod("pre", this);
      this.app.unregisterMethod("draw", this);
      this.app.unregisterMethod("post", this);
      this.app.unregisterMethod("mouseEvent", this);
      this.app.unregisterMethod("keyEvent", this);
    }
  }

  /**
  * Simple signature for background hides need to call app
  * @param col int
  */
  public void background(int col) {
    this.app.background(col);
  }

  /**
  * Simple signature for fill hides need to call app
  * @param col int
  */
  public void fill(int col) {
    this.app.fill(col);
  }

  /**
  * Simple signature for stroke hides need to call app
  * @param col int
  */
  public void stroke(int col) {
    this.app.stroke(col);
  }

  /**
  * Access applet if we must
  * @return applet PApplet
  */
  public PApplet app() {
    return this.app;
  }

  /**
  * required for processing
  */
  public void dispose() {
    setActive(false);
  }
}

```

### The ruby library

```ruby
java_import Java::MonkstoneCore::LibraryProxy
java_import Java::ProcessingEvent::KeyEvent
java_import Java::ProcessingEvent::MouseEvent
# classes that inherit from LibraryProxy are expected to implement
# the abstract draw method of monkstone.core.LibraryProxy the other methods are
# registered with PApplet instance in constructor ImplementingClass.new(app)
#
# def pre... NOOP can be overridden
# def draw... Abstract Method should be implemented NOOP is OK
# def post... NOOP can be overridden
# def keyEvent(e)... NOOP can be overridden
# def mouseEvent(e)... NOOP can be overridden
# `app` can be called to get PApplet instance

```

For example usage see [key_event.rb][key_event] and [my_library.rb][my_library]

[key_event]:https://github.com/ruby-processing/JRubyArt-examples/blob/master/processing_app/library/library_proxy/key_event.rb

[my_library]:https://github.com/ruby-processing/JRubyArt-examples/blob/master/processing_app/library/library_proxy/library/my_library/my_library.rb
