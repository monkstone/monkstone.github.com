---
layout: post
title: "Pixellation Beyond Photoshop / Gimp"
date: 2016-06-15 06:14:00
categories: jruby_art update
keywords: contextfreeart, pixellation, JRubyArt, file_chooser

---

This sketch features the new JRubyArt [chooser][file_chooser] library. But to make any sense requires that you have the [context free art][context] program installed. What this sketch does is analyse a images pixels and translates the information to a context free art sketch (consisting of haddock.cfdg and data.cfdg). You next use the context free art program to generate a image (movie) using haddock.cfdg (which loads data.cfdg). There are many opportunities to refine the cfdg sketch (you could replace circles with random shapes, but much more than that), or change the way the data.cfdg is produced, the possibilities are endless. One tip start with a smaller image than you want to create.

### pixellator.rb ###

{% highlight ruby %}
load_library :chooser
attr_reader :img, :data, :skip

def settings
  size 500, 500
end

def setup
  sketch_title 'Pixellator'
  color_mode(HSB, 360, 1.0, 1.0)
  resizable
  fill 0, 0, 200
  text('Click Window to Load Image', 10, 100)
  @skip = 5 # controls apparent resolution
  @data = []
end

def draw
  image(img, 0, 0) unless img.nil?
end

def write_data(name, data)
  df = "  %s [x %d y %d s %0.2f hue %d sat %0.3f brightness 1.0]\n"
  open(data_path('data.cfdg'), 'w') do |pw|
    pw.puts format("shape %s{\n", name)
    data.each do |row|
      pw.puts format(df, *row)
    end
    pw.puts "}\n"
  end
end

def write_start(start, data)
  open(data_path(format('%s.cfdg', start)), 'w') do |pw|
    pw.puts 'CF::Background = [b -1]'
    pw.puts format("startshape %s\n", start)
    pw.puts "shape dot{CIRCLE[]}\n"
    pw.puts "import data.cfdg\n"
  end
  write_data start, data
end

def file_selected(selection)
  if selection.nil?
    puts 'Nothing Chosen'
  else
    @img = load_image(selection.get_absolute_path)
    surface.set_size(img.width, img.height)
  end
end

def mouse_clicked
  @img = nil
  # java_signature 'void selectInput(String, String)'
  select_input('Select Image File', 'file_selected')
end

def key_pressed
  case key
  when 'p', 'P'
    export = Thread.new do
      pixellate
    end
    export.join
    puts 'done'
  when 's', 'S'
    save_frame(data_path('original.png'))
  else
    puts format('key %s was pressed', key)
  end
end

def pixellate
  load_pixels
  shp = 'dot'
  (skip...img.width).step(skip) do |x|
    (skip...img.height).step(skip) do |y|
      pix = pixels[x + y * width]
      sat = saturation(pix)
      hue = hue(pix)
      sz = brightness(pix) * skip
      data << [
        shp, -width / 2 + x, height / 2 - y, sz.round(2), hue, sat
      ] if sz > 0.4
    end
  end
  write_start 'haddock', data
end


{% endhighlight %}

My friend Phil Sutton RA painting (before). Phil was a contemporary of the late Harold Cohen, and told me that Harold used to paint with dots back in the day. Both artists are featured in [Six young Painters][six] The Arts Council (1956). Phil has remained resolutely a fauvist see some of his [work here][work] and some of his publicly owned work including some of his [Shakespeare paintings][shake].

<img src="/assets/before.png" />

My friend Phil Sutton RA painting after pixellation (size of dots is governed by brightness in this sketch)

<img src="/assets/after.png" />

[context]:http://www.contextfreeart.org
[jruby_art]:https://ruby-processing.github.io/index.html
[file_chooser]:http://ruby-processing.github.io/classes/chooser
[six]:https://www.amazon.co.uk/Six-Young-Painters-Michael-Andrews/dp/B00NRMKB6G
[work]:http://www.philipsuttonra.com/
[shake]:http://artuk.org/discover/artworks/search/actor:sutton-philip-b-1928
