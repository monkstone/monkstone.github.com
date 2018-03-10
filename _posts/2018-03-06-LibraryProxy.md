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
# by including Processing::Proxy we access sketch methods using method_missing
# however we use app.width, app.height and app.grid to access those methods and
# fill(int), stroke(int) from LibraryProxy class
class DeadGrid < LibraryProxy
  include Processing::Proxy
  attr_reader :app, :block_size, :grid, :width, :height

  def initialize(app)
    @app = app
    @block_size = app.block_size
    @width = app.width / block_size - 1
    @height = app.height / block_size - 1
    init_dead_grid(width, height)
  end

  def to_s
    "\nDeadGrid\n".tap do |string|
      string << "-" * width << "\n"
      grid.each do |row|
        string << row.inspect << "\n"
      end
    end
  end

  def init_dead_grid(width, height)
    @grid = (0..width).map do |row|
      (0..height).map { 0 }
    end
  end

  def draw
    stroke(color(255, 255, 255, 125))
    stroke_weight(1)
    app.grid(width, height) do |row, column| # jruby_art convenience method grid
      x = row * block_size
      y = column * block_size
      if (@grid[row][column] != 0)
        if (@grid[row][column] == 1)
          fill(color(255, 0, 0))
        elsif (@grid[row][column] == 2)
          fill(color(120, 200, 50))
        end
        rect(x, y, block_size, block_size)
      end
    end
  end

  def randomize
    app.grid(width, height) do |row, column| # jruby_art convenience method grid
      grid[row][column] = rand(0..1)
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
# by including Processing::Proxy we access sketch methods using method_missing
# for the block class that only `rect` and `color` since fill(int), stroke(int)
# are include in the LibraryProxy class
class Block < LibraryProxy
  include Processing::Proxy
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
    fill(color(55, 0, 255))
    rect(@x * block_size, @y * block_size, @width, @height)
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
