---
layout: post
title: "Using java library reflection methods"
date: 2016-01-08 18:54:00
categories: jruby_art update
keywords: carnivore, sniffer, reflection, interface, JRubyArt
permalink: _posts/carnivore
---

It is an unfortunate truth that `java reflection methods` are popular with both processing developers and the developers of supporting libraries. It is unfortunate because we have to go through hoops to use these methods in JRubyArt (and ruby-processing). The `captureEvent` and `videoEvent` are examples of reflection methods from the processing video developers. But we have made these readily available to JRubyArt users as a [simple library][library] load `load_library :video_event`. This is what you would do (if we had not created the `video_event` library):-

- create a java class `VideoInterface`
- compile the java class (including classpath)
- create a jar (and place it in the same folder as your sketch)
- modify the sketch to both `require` the `jar` and to `include` the interface. 

###VideoInterface.java
See [javadoc here][video]
{% highlight java %}
package monkstone.videoevent;
import processing.video.Movie;
import processing.video.Capture;
/**
 * This interface makes it easier/possible to use the reflection methods
 * from Movie and Capture classes in Processing::App in JRubyArt
 * @author Martin Prout
 */
public interface VideoInterface {
    /**
     * Used to implement reflection method in PApplet
     * @see processing.video.Movie
     * @param movie Movie
     */
    public void movieEvent(Movie movie);
    /**
     * Used to implement reflection method in PApplet
     * @see processing.video.Capture
     * @param capture Capture
     */
    public void captureEvent(Capture capture);    
}
{% endhighlight %}

To match packaging the java file needs to be nested in `monkstone/videoevent` folders (it is unwise not to have a package) and compile and jar as follows, requires jdk8+.

{% highlight bash %}
# NB: classpath needs to be a fully qualified path to jars (not as below)
javac -cp video.jar:core.jar monkstone/videoevent/VideoInterface.java

jar -cvf video_event.jar monkstone
{% endhighlight %}

See below a sketch which using this VideoEvent interface, see the version using the JRubyArt provided `:video_event` library [here][bw]

###black_white_capture.rb
{% highlight ruby %}
require_relative 'video_event'
load_libraries :video
include_package 'processing.video'

include Java::MonkstoneVideoevent::VideoInterface

attr_reader :cam, :my_shader

def setup
  sketch_title 'Black & White Capture'
  @my_shader = load_shader('bwfrag.glsl')
  start_capture(width, height)
end

def start_capture(w, h)
  @cam = Capture.new(self, w, h)
  cam.start
end

def draw
  image(cam, 0, 0)
  return if mouse_pressed?
  filter(my_shader)
end

# using snake case to match java reflection method
def captureEvent(c)
  c.read
end

def settings
  size(960, 544, P2D)
end
{% endhighlight %}

Now where this knowledge becomes really useful, is when you want to use another library, say the vanilla processing carnivore library whose `packetEvent` method also depends on java relection. Here is a suitable CarnivoreListener class.

###CarnivoreListener.java
{% highlight java %}

package monkstone;

/*
* Best to use a package to avoid namespace clashes, create your own
*/
public interface CarnivoreListener {
  /*
  * @param packet the CarnivorePacket, a reflection method
  */
  public void packetEvent(org.rsg.carnivore.CarnivorePacket packet);  
}
{% endhighlight %}

Which we compile as before

{% highlight bash %}
# NB: classpath needs to be a fully qualified path to jar (not as below)
javac -cp carnivore.jar monkstone/CarnivoreListener.java

jar -cvf carnivore_listener.jar monkstone
{% endhighlight %}

Here is an example sketch:-

###carnivore1.rb

{% highlight ruby %}
# A Simple Carnivore Client -- print packets in Processing console
#
# Note: requires Carnivore Library for Processing (http://r-s-g.org/carnivore)
#
# + Windows:  first install winpcap (http://winpcap.org)
# + Mac:      first open a Terminal and execute this commmand: sudo chmod 777 /dev/bpf*
#             you need to do this each time you reboot Mac
# + Linux:    run with difficulty (run with sudo rights arghh!!!) also install libpcap 

load_library :carnivore
include_package 'org.rsg.carnivore'
java_import 'org.rsg.lib.Log'
require_relative 'carnivore_listener'

include Java::Monkstone::CarnivoreListener

attr_reader :c

def settings
  size(600, 400)
end

def setup
  sketch_title 'Carnivore Example'
  background(255)  
  @c = CarnivoreP5.new(self)
  Log.setDebug(true) # comment out for quiet mode
  # c.setVolumeLimit(4) #limit the output volume (optional)
  # c.setShouldSkipUDP(true) #tcp packets only (optional)
end

def draw
end

# Called each time a new packet arrives
def packetEvent(p)
  puts(format('(%s packet) %s > %s', p.strTransportProtocol, p.senderSocket, p.receiverSocket))
  # puts(format('Payload: %s', p.ascii))
  # puts("---------------------------\n")
end
{% endhighlight %}

Read more about calling java from ruby including [reflection here][reflection].

[video]:http://ruby-processing.github.io/JRubyArt/monkstone/videoevent/VideoInterface.html
[library]:https://github.com/ruby-processing/JRubyArt/blob/master/library/video_event/video_event.rb
[reflection]:https://github.com/jruby/jruby/wiki/CallingJavaFromJRuby
[bw]:https://github.com/ruby-processing/JRubyArt-examples/blob/master/processing_app/library/video/capture/black_white_capture.rb
