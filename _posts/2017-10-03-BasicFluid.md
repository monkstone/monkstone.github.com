---
layout: post
title: "Basic Fluid Sketch in JRuby"
date: 2017-10-03 08:00:00
categories: jruby_art update
keywords: ruby-processing, JRubyArt, propane, jruby
---
Here is another [PixelFlow library][pixgit] sketch by [Thomas Diewald][diewald]:-

```ruby
#
# PixelFlow | Copyright (C) 2016-17 Thomas Diewald (www.thomasdiewald.com)
# Translated to propane by Martin Prout
#
# src  - www.github.com/diwi/PixelFlow
#
# A Processing/Java library for high performance GPU-Computing.
# MIT License: https://opensource.org/licenses/MIT
#
load_libraries :PixelFlow, :controlP5

java_import 'com.thomasdiewald.pixelflow.java.DwPixelFlow'
java_import 'com.thomasdiewald.pixelflow.java.fluid.DwFluid2D'
java_import 'controlP5.Accordion'
java_import 'controlP5.ControlP5'
java_import 'controlP5.Group'
java_import 'controlP5.RadioButton'
java_import 'controlP5.Toggle'
java_import 'controlP5.ControlListener'

include ControlListener

DISPLAY_MODE = %w[Density Temperature Pressure Velocity]

# This example shows a very basic fluid simulation setup.
# Multiple emitters add velocity/temperature/density each iteration.
# Obstacles are added at startup, by just drawing into a usual object.
# The same way, obstacles can be added/removed dynamically.
#
# additionally some locations add temperature to the scene, to show how
# buoyancy works.
#
#
# controls:
#
# LMB: add Density + Velocity
# MMB: draw obstacles
# RMB: clear obstacles

VIEWPORT_W = 800
VIEWPORT_H = 800
BACKGROUND_COLOR = 0


attr_reader :context, :fluid, :obstacle_painter, :pg_fluid, :pg_obstacles
attr_reader :update_fluid, :display_fluid_textures, :display_fluid_vectors
attr_reader :display_fluid_texture_mode, :cp5, :fluidgrid_scale, :gui_w
attr_reader :gui_x, :gui_y, :mouse_input, :rb_setdisplay_mode, :group_fluid
attr_reader :background_color, :iterations
def settings
  size(VIEWPORT_W, VIEWPORT_H, P2D)
  smooth(2)
end

def setup
  @update_fluid               = true
  @display_fluid_textures     = true
  @display_fluid_vectors      = false
  @display_fluid_texture_mode = 0
  @background_color = 0
  # main library context
  @context = DwPixelFlow.new(self)
  context.print
  context.printGL
  @fluidgrid_scale = 1
  @gui_w = 200
  @gui_x = 20
  @gui_y = 20

  # fluid simulation
  @fluid = DwFluid2D.new(context, VIEWPORT_W, VIEWPORT_H, fluidgrid_scale)
  # interface for adding data to the fluid simulation
  fluid.addCallback_FluiData do
   # add impulse: density + temperature
    intensity = 1.0
    px = 1 * width / 3
    py = 0
    radius = 100
    r = 0.0
    g = 0.3
    b = 1.0
    fluid.add_density(px, py, radius, r, g, b, intensity)

    if (fluid.simulation_step % 200).zero?
      temperature = 50.0
      fluid.addTemperature(px, py, radius, temperature)
    end

    # add impulse: density + temperature
    animator = sin(fluid.simulation_step * 0.01)

    intensity = 1.0
    px = 2 * width / 3.0
    py = 150
    radius = 50
    r = 1.0
    g = 0.0
    b = 0.3
    fluid.add_density(px, py, radius, r, g, b, intensity)
    temperature = animator * 20.0
    fluid.addTemperature(px, py, radius, temperature)
    # add impulse: density
    px = 1 * width / 3.0
    py = height - 2 * height / 3.0
    radius = 50.5
    r = g = b = 64  / 255.0
    intensity = 1.0
    fluid.add_density(px, py, radius, r, g, b, intensity, 3.0)
    # add impulse: density + velocity
    if mouse_input && mouse_button == LEFT
      radius = 15
      vscale = 15
      px     = mouse_x
      py     = height-mouse_y
      vx     = (mouse_x - pmouse_x) * +vscale
      vy     = (mouse_y - pmouse_y) * -vscale
      fluid.add_density(px, py, radius, 0.25, 0.0, 0.1, 1.0)
      fluid.add_velocity(px, py, radius, vx, vy)
    end
  end
  # pgraphics for fluid
  @pg_fluid = create_graphics(VIEWPORT_W, VIEWPORT_H, P2D)
  pg_fluid.smooth(4)
  pg_fluid.begin_draw
  pg_fluid.background(background_color)
  pg_fluid.end_draw
  # pgraphics for obstacles
  @pg_obstacles = create_graphics(VIEWPORT_W, VIEWPORT_H, P2D)
  pg_obstacles.smooth(0)
  pg_obstacles.begin_draw
  pg_obstacles.clear
  # circle-obstacles
  pg_obstacles.stroke_weight(10)
  pg_obstacles.noFill
  pg_obstacles.no_stroke
  pg_obstacles.fill(64)
  radius = 100
  pg_obstacles.ellipse(1 * width / 3.0,  2 * height / 3.0, radius, radius)
  radius = 150
  pg_obstacles.ellipse(2 * width / 3.0,  2 * height / 4.0, radius, radius)
  radius = 200
  pg_obstacles.stroke(64)
  pg_obstacles.stroke_weight(10)
  pg_obstacles.noFill
  pg_obstacles.ellipse(1 * width / 2.0,  1 * height / 4.0, radius, radius)
  # border-obstacle
  pg_obstacles.stroke_weight(20)
  pg_obstacles.stroke(64)
  pg_obstacles.noFill
  pg_obstacles.rect(0, 0, pg_obstacles.width, pg_obstacles.height)
  pg_obstacles.end_draw
  createGUI
  # class, that manages interactive drawing (adding/removing) of obstacles
  @obstacle_painter = ObstaclePainter.new(pg_obstacles)
  @mouse_input = !cp5.isMouseOver && mousePressed && !obstacle_painter.drawing?
  frame_rate(60)
end

def draw
  # update simulation
  if update_fluid
    fluid.addObstacles(pg_obstacles)
    fluid.update
  end
  # clear render target
  pg_fluid.begin_draw
  pg_fluid.background(background_color)
  pg_fluid.end_draw
  # render fluid stuff
  if display_fluid_textures
    # render: density (0), temperature (1), pressure (2), velocity (3)
    fluid.renderFluidTextures(pg_fluid, display_fluid_texture_mode)
  end
  # render: velocity vector field
  fluid.renderFluidVectors(pg_fluid, 10) if display_fluid_vectors
  # display
  image(pg_fluid, 0, 0)
  image(pg_obstacles, 0, 0)
  obstacle_painter.display_brush(g)
  # info
  format_string = 'Fluid Basic [size %d/%d]  [frame %d]  [fps: (%6.2f)]'
  surface.set_title(format(format_string, fluid.fluid_w, fluid.fluid_h, fluid.simulation_step, frame_rate))
end

def mouse_pressed
  obstacle_painter.begin_draw(1) if mouse_button == CENTER # add obstacles
  obstacle_painter.begin_draw(2) if mouse_button == RIGHT # remove obstacles
end

def mouse_dragged
  obstacle_painter.draw
end

def mouse_released
  obstacle_painter.end_draw
end

def resize_up
  @fluidgrid_scale -= 1 unless fluidgrid_scale < 2
  fluid.resize(width, height, fluidgrid_scale)
end

def resize_down
  @fluidgrid_scale += 1
  fluid.resize(width, height, fluidgrid_scale)
end

def reset!
  fluid.reset
end

def toggle_pause
  @update_fluid = !update_fluid
end

def display_mode(val)
  @display_fluid_texture_mode = val
  @display_fluid_textures = display_fluid_texture_mode != -1
end

def display_velocity_vectors(val)
  @display_fluid_vectors = val != -1
end

def key_released
  case key
  when 'p'
    toggle_pause # pause / unpause simulation
  when 'r'
    reset!
  when 's'
    cp5.save_properties(data_path('fluid_basic.json'))
  end
end

def createGUI
  @cp5 = ControlP5.new(self)
  sx = 100
  sy = 14
  oy = (sy * 1.5).to_i
  ######################################
  # GUI - FLUID
  ######################################
  group_fluid = cp5.addGroup('fluid')
  group_fluid.setHeight(20)
             .setSize(gui_w, 300)
             .setBackgroundColor(color(16, 180))
             .setColorBackground(color(16, 180))
  group_fluid.getCaptionLabel.align(CENTER, CENTER)
  px = 10
  py = 15
  cp5.addButton('reset')
     .setGroup(group_fluid)
     .setSize(80, 18)
     .setPosition(px, py)
     .addListener { reset! }
  cp5.addButton('+')
     .setGroup(group_fluid)
     .setSize(39, 18)
     .setPosition(px += 82, py)
     .addListener { resize_up }
  cp5.addButton('-')
     .setGroup(group_fluid)
     .setSize(39, 18)
     .setPosition(px += 41, py)
     .addListener { resize_down }
  px = 10
  cp5.addSlider('velocity')
     .setGroup(group_fluid)
     .setSize(sx, sy)
     .setPosition(px, py += (oy*1.5).to_i)
     .setRange(0, 1)
     .setValue(1.0)
  cp5.addSlider('density')
     .setGroup(group_fluid)
     .setSize(sx, sy)
     .setPosition(px, py += oy)
     .setRange(0, 1)
     .setValue(0.99)
  cp5.addSlider('temperature')
     .setGroup(group_fluid)
     .setSize(sx, sy)
     .setPosition(px, py += oy)
     .setRange(0, 1)
     .setValue(0.8)
  cp5.addSlider('vorticity')
     .setGroup(group_fluid)
     .setSize(sx, sy)
     .setPosition(px, py += oy)
     .setRange(0, 1)
     .setValue(0.1)
  cp5.addSlider('iterations')
     .setGroup(group_fluid)
     .setSize(sx, sy)
     .setPosition(px, py += oy)
     .setRange(0, 80)
     .setValue(40)
  cp5.addSlider('timestep')
     .setGroup(group_fluid).setSize(sx, sy).setPosition(px, py += oy)
     .setRange(0, 1)
     .setValue(0.125)
  cp5.addSlider('gridscale')
     .setGroup(group_fluid)
     .setSize(sx, sy)
     .setPosition(px, py += oy)
     .setRange(0, 50)
     .setValue(1)
     @rb_setdisplay_mode = cp5.addRadio('display_mode')
                             .setGroup(group_fluid)
                             .setSize(80, 18)
                             .setPosition(px, py += (oy*1.5).to_i)
                             .setSpacingColumn(2)
                             .setSpacingRow(2)
                             .setItemsPerRow(2)
  DISPLAY_MODE.each_with_index do |item, i|
    rb_setdisplay_mode.addItem item, i
  end
  rb_setdisplay_mode.getItems.each do |toggle|
    toggle.getCaptionLabel.alignX(CENTER)
  end
  cp5.addRadio('display_velocity_vectors')
     .setGroup(group_fluid)
     .setSize(18,18)
     .setPosition(px, py += (oy*2.5).to_i)
     .setSpacingColumn(2)
     .setSpacingRow(2)
     .setItemsPerRow(1)
     .addItem('Velocity Vectors', 0)
     #.activate(display_fluid_vectors ? 0 : 2)
  ##########################################
  # GUI - DISPLAY
  ##########################################
  group_display = cp5.addGroup('display')
  group_display.setHeight(20)
               .setSize(gui_w, 50)
               .setBackgroundColor(color(16, 180))
               .setColorBackground(color(16, 180))
  group_display.getCaptionLabel.align(CENTER, CENTER)
  px = 10
  py = 15
  cp5.addSlider('background')
     .setGroup(group_display)
     .setSize(sx,sy)
     .setPosition(px, py)
     .setRange(0, 255)
     .setValue(0)
  ##########################################
  # GUI - ACCORDION
  ##########################################
  cp5.addAccordion('acc').setPosition(gui_x, gui_y)
                         .setWidth(gui_w)
                         .setSize(gui_w, height)
                         .setCollapseMode(Accordion::MULTI)
                         .addItem(group_fluid)
                         .addItem(group_display)
                         .open(4)
end

def controlEvent(event)
  if event.group?
    display_mode(rb_setdisplay_mode.getValue) if event.getGroup.getName == 'display_mode'
    display_velocity_vectors(event.getGroup.getValue) if event.getGroup.getName == 'display_velocity_vectors'
  elsif event.controller?
    case event.getController.getName
    when 'gridscale'
      @fluidgrid_scale = event.getController.getValue.to_i
    when 'velocity'
      fluid.param.dissipation_velocity = event.getController.getValue
    when 'background'
      @background_color = event.getController.getValue
    when 'temperature'
      fluid.param.dissipation_temperature = event.getController.getValue
    when 'timestep'
      fluid.param.timestep = event.getController.getValue
    when 'iterations'
      fluid.param.num_jacobi_projection = event.getController.getValue
    when 'density'
      fluid.param.dissipation_density = event.getController.getValue
    when 'vorticity'
      fluid.param.vorticity = event.getController.getValue
    end
  end
end

class ObstaclePainter
  include Processing::Proxy
  # 0 ... not drawing
  # 1 ... adding obstacles
  # 2 ... removing obstacles
  attr_reader :pg, :draw_mode, :size_paint, :size_clear, :shading
  attr_reader :paint_x, :paint_y, :clear_x, :clear_y

  def initialize(pg)
    @pg = pg
    @draw_mode = 0
    @size_paint = 15
    @size_clear = size_paint * 2.5
    @shading = 64
  end

  def begin_draw(mode)
    @paint_x = mouse_x
    @paint_y = mouse_y
    @draw_mode = mode
    if mode == 1
      pg.begin_draw
      pg.blend_mode(REPLACE)
      pg.no_stroke
      pg.fill(shading)
      pg.ellipse(mouse_x, mouse_y, size_paint, size_paint)
      pg.end_draw
    end
    clear(mouse_x, mouse_y) if mode == 2
  end

  def drawing?
    !draw_mode.zero?
  end

  def draw
    @paint_x = mouse_x
    @paint_y = mouse_y
    if draw_mode == 1
      pg.begin_draw
      pg.blend_mode(REPLACE)
      pg.stroke_weight(size_paint)
      pg.stroke(shading)
      pg.line(mouse_x, mouse_y, pmouse_x, pmouse_y)
      pg.end_draw
    end
    clear(mouse_x, mouse_y) if draw_mode == 2
  end

  def end_draw
    @draw_mode = 0
  end

  def clear(x, y)
    @clear_x = x
    @clear_y = y
    pg.begin_draw
    pg.blend_mode(REPLACE)
    pg.no_stroke
    pg.fill(0, 0)
    pg.ellipse(x, y, size_clear, size_clear)
    pg.end_draw
  end

  def display_brush(dst)
    if draw_mode == 1
      dst.stroke_weight(1)
      dst.stroke(0)
      dst.fill(200, 50)
      dst.ellipse(paint_x, paint_y, size_paint, size_paint)
    elsif draw_mode == 2
      dst.stroke_weight(1)
      dst.stroke(200)
      dst.fill(200, 100)
      dst.ellipse(clear_x, clear_y, size_clear, size_clear)
    end
  end
end

```

<img src="/assets/basic_fluid.png" />

[pixgit]:https://github.com/diwi/PixelFlow
[diewald]:http://thomasdiewald.com/blog/
