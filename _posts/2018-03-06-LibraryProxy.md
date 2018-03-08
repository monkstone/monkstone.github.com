---
layout: post
title: "Sketch Using Library Proxy in JRubyArt"
date: 2018-03-06 06:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, squarism, observable, observer
---
### A ruby-processing sketch by Squarism translated to JRubyArt

In this [JRubyArt][jruby_art] sketch after an original ruby-processing version by [Chris Dillon][squarism] we create a local ruby library, and make use of `LibraryProxy` to access the draw loop from our custom classes.
The sketch:-

```ruby
require 'observer'
include Observable
load_libraries :library_proxy, :dead_grid
attr_accessor :deadgrid, :player, :block_size
SPACE = 32

def settings
  size 256, 256
end

def setup
  sketch_title 'Dead Grid Events'
  @block_size = 16
  @deadgrid = DeadGrid.new self
  @player = Block.new self
  puts deadgrid
  add_observer(BlockedHandler.new)
end

def draw
  background 0
  draw_grid_lines
end

def draw_grid_lines
  stroke_weight 1
  stroke 40
  # for each block in deadgrid, draw lines using JRubyArt `grid` utility
  grid(width, height, block_size, block_size) do |x, y|
    line(x, 0, x, height)
    line(0, y, width, y)
  end
end

def key_pressed
  case key_code
  when SPACE
    puts 'Randomize.'
    deadgrid.randomize
    # if grid spawns under player, remove it
    unless deadgrid.grid[player.x][player.y].zero?
      deadgrid.grid[player.x][player.y] = 0
    end
    return
  when LEFT
    player.direction = 0
    return unless player.x > 0
    return player.x -= 1 if deadgrid.can_move?(player, 1)
  when RIGHT
    player.direction = 2
    # check bounds
    return unless player.x < deadgrid.width
    return player.x += 1 if deadgrid.can_move?(player, 1)
  when UP
    player.direction = 1
    # check bounds
    return unless player.y > 0
    return player.y -= 1 if deadgrid.can_move?(player, 1)
  when DOWN
    player.direction = 3
    return unless player.y < deadgrid.height
    return player.y += 1 if deadgrid.can_move?(player, 1)
  end
  changed
  notify_observers(player, deadgrid)
end

```

The library (in `library/dead_grid/dead_grid.rb`)

```ruby
# By inheriting from LibraryProxy draw loop is by reflection sketch draw loop
class DeadGrid < LibraryProxy
  attr_reader :app, :block_size
  attr_accessor :grid, :width, :height

  def initialize(app)
    @app = app
    @block_size = app.block_size
    @width = app.width / block_size - 1
    @height = app.height / block_size - 1
    @grid = Array.new
    0.upto(width) do |i|
      grid[i] = Array.new
      0.upto(height) { |j| @grid[i][j] = 0 }
    end
  end

  def to_s
    string = ""
    string << "\nDeadGrid\n"
    string << "-" * width << "\n"
    0.upto(width) do |i|
      0.upto(height) { |j| string << grid[j][i].to_s }
      string << "\n"
    end
    string
  end

  def draw
    app.stroke(255, 255, 255, 125)
    app.stroke_weight(1)
    0.upto(width) do |i|
      0.upto(height) do |j|
        x = i * block_size
        y = j * block_size

        if (@grid[i][j] != 0)
          if (@grid[i][j] == 1)
            app.fill(255, 0, 0)
          elsif (@grid[i][j] == 2)
            app.fill(120, 200, 50)
          end
          app.rect(x,y,block_size,block_size)
        end
      end
    end
  end

  def randomize
    0.upto(width) do |i|
      0.upto(height) do |j|
        @grid[i][j] = rand(0..1)
      end
    end
  end

  def can_move?(player, distance)
    case player.direction
    when 0 # left
      check_x = player.x - distance
      check_y = player.y
      return !there?(check_x, check_y) unless player.x.zero?
      false
    when 2 # right
      check_x = player.x + distance
      check_y = player.y
      return !there?(check_x, check_y) if player.x < grid.size
      false
    when 1 # up
      check_x = player.x
      check_y = player.y - distance
      return !there?(check_x, check_y) if player.y > 0
      false
    when 3 # down
      check_x = player.x
      check_y = player.y + distance
      return !there?(check_x, check_y) if player.y <= grid.size
      false
    end
  end

  def there?(x, y)
    grid[x][y] != 0
  end
end

# By inheriting from LibraryProxy draw loop is by reflection sketch draw loop
class Block < LibraryProxy
  attr_reader :app, :block_size
  attr_accessor :x, :y, :width, :height, :direction

  def initialize(app)
    @app = app
    @block_size = app.block_size
    @x = 7
    @y = 7
    @width = block_size
    @height = block_size
    @direction = 0
  end

  def draw
    app.fill(55, 0, 255)
    app.rect(@x * block_size, @y * block_size, @width, @height)
  end
end

# figures out which block to highlight on grid when player can't move
class BlockedHandler
  def update(player, deadgrid)
    case player.direction
    when 0
      return unless deadgrid.grid[player.x - 1][player.y] == 1
      deadgrid.grid[player.x - 1][player.y] = 2
    when 1
      return unless deadgrid.grid[player.x][player.y - 1] == 1
      deadgrid.grid[player.x][player.y - 1] = 2
    when 2
      return unless  deadgrid.grid[player.x + 1][player.y] == 1
      deadgrid.grid[player.x + 1][player.y] = 2
    when 3
      return unless  deadgrid.grid[player.x][player.y + 1] == 1
      deadgrid.grid[player.x][player.y + 1] = 2
    end
  end
end

```

### Running the sketch from atom

![squarism.png]({{site.github.url}}/assets/squarism.png)

[squarism]:http://squarism.com/2011/04/17/simple-ruby-processing-collision-detection/
[jruby_art]:https://ruby-processing.github.io/JRubyArt/
