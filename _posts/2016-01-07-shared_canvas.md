---
layout: post
title: "Shared canvas net"
date: 2016-01-07 21:50:00
categories: jruby_art update
keywords: client, server, net, JRubyArt
permalink: _posts/net
---

Here is a vanilla processing sketch (translated to JRubyArt) that uses the processing net library.

###shared_canvas_server.rb

{% highlight ruby %}
# Shared Drawing Canvas (Server)
# by Alexander R. Galloway. Translated to JRubyArt by Martin Prout
#
# A server that shares a drawing canvas between two computers.
# In order to open a socket connection, a server must select a
# port on which to listen for incoming clients and through which
# to communicate. Once the socket is established, a client may
# connect to the server and send or receive commands and data.
# Get this program running and then start the Shared Drawing
# Canvas (Client) program so see how they interact.
#
load_library :net
include_package 'processing.net'

attr_reader :s

def settings
  size(450, 255)
end

def setup
  sketch_title 'Server'
  background(204)
  stroke(0)
  frame_rate(5) # Slow it down a little
  @s = Server.new(self, 12_345) # Start a simple server on a port
end

def draw
  if mouse_pressed?
    # Draw our line
    stroke(255)
    line(pmouse_x, pmouse_y, mouse_x, mouse_y)
    # Send mouse coords to other person
    s.write(format("%d %d %d %d\n", pmouse_x, pmouse_y, mouse_x, mouse_y))
  end
  # Receive data from client
  c = s.available
  return if c.nil?
  input = c.read_string
  # Split values into an array and convert to int
  data = input.split(' ').map(&:to_i)
  # Draw line using received coords
  line(*data)
end
{% endhighlight %}

###shared_canvas_client.rb

{% highlight ruby %}
# Shared Drawing Canvas (Client)
# by Alexander R. Galloway. Translated to JRubyArt by Martin Prout
#
# The Processing Client class is instantiated by specifying a remote address
# and port number to which the socket connection should be made. Once the
# connection is made, the client may read (or write) data to the server.
# NB: Start the Shared Drawing Canvas (Server) program first
#
load_library :net
include_package 'processing.net'

attr_reader :c

def settings
  size(450, 255)
end

def setup
  sketch_title 'Client'
  background(204)
  stroke(0)
  frame_rate(5) # Slow it down a little
  # Replace with your server's IP and port
  @c = Client.new(self, '127.0.0.1', 12_345)
end

def draw
  if mouse_pressed?
    # Draw our line
    stroke(255)
    line(pmouse_x, pmouse_y, mouse_x, mouse_y)
    # Send mouse coords to other person
    c.write(format("%d %d %d %d\n", pmouse_x, pmouse_y, mouse_x, mouse_y))
  end
  # Receive data from server
  return unless c.available > 0
  input = c.read_string
  # Split values into an array and convert to int
  data = input.split(' ').map(&:to_i)
  # Draw line using received coords
  stroke(0)
  line(*data)
end
{% endhighlight %}

