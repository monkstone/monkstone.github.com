---
layout: post
title:  "Generative Gestaltung Mesh Sketch in JRubyArt"
date:   2015-10-12 17:07:00
categories: jruby_art update
---

Here is another examples from [Generative Gestaltung, ISBN: 978-3-87439-759-9][generative], which makes use of an advanced `Vec3D` feature `:to_vertex`, not available for `PVector`. Also used first class and required keyword arguments not available with ruby-processing.  ...see already [translated sketches here][sketches], why not show off your ruby chops by porting more examples to JRubyArt....

And send me a tweet @[monkstoneT][twitter]

Here is the sketch code:-

{% highlight ruby %}
# m_3_3_03.rb
#
# Generative Gestaltung, ISBN: 978-3-87439-759-9
# First Edition, Hermann Schmidt, Mainz, 2009
# Hartmut Bohnacker, Benedikt Gross, Julia Laub, Claudius Lazzeroni
# Copyright 2009 Hartmut Bohnacker, Benedikt Gross, Julia Laub, Claudius Lazzeroni
# 
# ruby port by Martin Prout (for JRubyArt)
#
# http://www.generative-gestaltung.de
#
# Licensed under the Apache License, Version 2.0 (the "License")
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.


require_relative 'mesh'
include GeometricForm

U_RANGE = (-PI..PI)
V_RANGE = (-PI..PI)
MAX_V = 200
MAX_U = 200

attr_reader :renderer, :points

def settings
  size 300, 300 , P3D
end

def setup
  sketch_title 'Geometric Forms'
  ArcBall.init(self)
  @renderer = AppRender.new(self)
  @points = generate_points(MAX_U, MAX_V)
  no_stroke
  fill rand(255),rand(255), rand(255)
end

def draw
  background 200
  setup_lights
  (MAX_V - 1).times do |iv|
    begin_shape(TRIANGLE_STRIP)
    (MAX_U).times do |iu|
      points[iv][iu].to_vertex(renderer)
      points[iv + 1][iu].to_vertex(renderer)
    end
    end_shape
  end
end

def setup_lights
  lights
  light_specular(230, 230, 230)
  directional_light(200, 200, 200, 0.5, 0.5, -1)
  specular(color(200))
  shininess(5.0)
end

# Edit this method to choose different shape
def generate_points(u_count, v_count)
  points = []
  v_count.times do |iv|
    row = []
    u_count.times do |iu|
      u = map1d(iu, (0..u_count), U_RANGE)
      v = map1d(iv, (0..v_count), V_RANGE)
      # default scale: 50, param: Array.new(12, 1) and mesh_distortion: 0
      row << superformula(u: u, v: v)
    end
    points << row
  end
  points
end                                                                                                                                                                                     
{% endhighlight %}

Here is the mesh code:-

{% highlight ruby %}
# mesh.rb
module GeometricForm
  include Math

  def astroidal_ellipsoid(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    u /= 2
    x = 3 * (cos(u) * cos(v))**(3 * params[0])
    y = 3 * (sin(u) * cos(v))**(3 * params[0])
    z = 3 * sin(v)**(3*params[0])
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def bohemian_dome(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = 2 * cos(u)
    y = 2 * sin(u) + params[0] * cos(v)
    z = 2 * sin(v)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def bow(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    u /= PI * 2
    v /= PI * 2
    x = (2 + params[0] * sin(PI * 2 * u)) * sin(PI * 4 * v)
    y = (2 + params[0] * sin(PI * 2 * u)) * cos(PI * 4 * v)
    z = params[0] * cos(PI * 2 * u) + 3 * cos(PI * 2 * v)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def corkscrew(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = cos(u) * cos(v)
    y = sin(u) * cos(v)
    z = sin(v) + params[0] * u
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def elliptic_torus(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = 1.5 * (params[0] + cos(v)) * cos(u)
    y = 1.5 * (params[0] + cos(v)) * sin(u)
    z = 1.5 * sin(v) + cos(v)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def figure8torus(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = 1.5 * cos(u) * (params[0] + sin(v) * cos(u) - sin(2 * v) * sin(u) / 2)
    y = 1.5 * sin(u) * (params[0] + sin(v) * cos(u) - sin(2 * v) * sin(u) / 2)
    z = 1.5 * sin(u) * sin(v) + cos(u) * sin(2 * v) / 2
  return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def horn(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    uc = u / PI
    # v /= PI
    x = (2 * params[0] + uc * cos(v)) * sin(2 * u)
    y = (2 * params[0] + uc * cos(v)) * cos(2 * u) + 2 * uc
    z = uc * sin(v)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale if mesh_distortion == 0 + Vec3D.random * mesh_distortion
  end

  def kidney(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    u /= 2
    x = cos(u) * (params[0] * 3 * cos(v) - cos(3 * v))
    y = sin(u) * (params[0] * 3 * cos(v) - cos(3 * v))
    z = 3 * sin(v) - sin(3 * v)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale if mesh_distortion == 0 + Vec3D.random * mesh_distortion
  end

  def lemniscape(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    u /= 2
    cosvSqrtAbsSin2u = cos(v)*sqrt((sin(2*params[0]*u)).abs)
    x = cosvSqrtAbsSin2u*cos(u)
    y = cosvSqrtAbsSin2u*sin(u)
    z = 3 * (x**2 - y**2 + 2 * x * y * tan(v)**2)
    x *= 3
    y *= 3
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale if mesh_distortion == 0 + Vec3D.random * mesh_distortion
  end

  def limpet_torus(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = 1.5 * params[0] * cos(u) / (sqrt(2) + sin(v))
    y = 1.5 * params[0] * sin(u) / (sqrt(2) + sin(v))
    z = 1.5 * 1 / (sqrt(2) + cos(v))
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale if mesh_distortion == 0 + Vec3D.random * mesh_distortion
  end

  def maeders_owl(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = 0.4 * (v * cos(u) - 0.5 * params[0] * v**2 * cos(2 * u))
    y = 0.4 * (-v * sin(u) - 0.5 * params[0] * v**2 * sin(2 * u))
    z = 0.4 * (4 * v**1.5) * cos(3 * u / 2) / 3
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale if mesh_distortion == 0 + Vec3D.random * mesh_distortion
  end

  def paraboloid(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    pd = params[0]
    pd = 0.0001 if (pd == 0)
    x = (v / pd)**0.5 * sin(u)
    y = v
    z = (v / pd)**0.5 * cos(u)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale if mesh_distortion == 0 + Vec3D.random * mesh_distortion
  end

  def shell(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = params[1] * (1 - (u / PI * 2)) * cos(params[0] * u) * (1 + cos(v)) + params[3] * cos(params[0] * u)
    y = params[1] * (1 - (u / PI * 2)) * sin(params[0] * u) * (1 + cos(v)) + params[3] * sin(params[0] * u)
    z = params[1] * (u / PI * 2) + params[0] * (1 - (u / PI * 2)) * sin(v)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def sine(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = 2 * sin(u)
    y = 2 * sin(params[0] * v)
    z = 2 * sin(u + v)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def sphere(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    vc = (2.0 * v) + PI / 2.0
    x = 2 * (sin(vc) * sin(u))
    y = 2 * (params[0] * cos(vc))
    z = 2 * (sin(vc) * cos(u))
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end
    
  def steinbach_screw(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = u * cos(v)
    y = u * sin(params[0] * v)
    z = v * cos(u)
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def superformula(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    v /= 2
    # Superformel 1
    a = params[0]
    b = params[1]
    m = params[2]
    n1 = params[3]
    n2 = params[4]
    n3 = params[5]
    r1 = ((cos(m * u / 4) / a).abs**n2 + (sin(m * u / 4) / b).abs**n3)**(-1 / n1)
    # Superformel 2
    a = params[6]
    b = params[7]
    m = params[8]
    n1 = params[9]
    n2 = params[10]
    n3 = params[11]
    r2 = ((cos(m * v / 4) / 2).abs**n2 + (sin(m * v / 4) / b).abs**n3)**(-1 / n1)
    x = 2 * (r1 * sin(u) * r2 * cos(v))
    y = 2 * (r2 * sin(v))
    z = 2 * (r1 * cos(u) * r2 * cos(v))
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def torus(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = 1 * ((params[1] + 1 + params[0] * cos(v)) * sin(u))
    y = 1 * (params[0] * sin(v))
    z = 1 * ((params[1] + 1 + params[0] * cos(v)) * cos(u))
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end

  def trianguloid(u:, v:, mesh_distortion: 0, scale: 50, params: Array.new(12, 1))
    x = 0.75 * (sin(3 * u) * 2 / (2 + cos(v)))
    y = 0.75 * ((sin(u) + 2 * params[0] * sin(2 * u)) * 2 / (2 + cos(v + PI * 2 )))
    z = 0.75 * ((cos(u) - 2 * params[0] * cos(2 * u)) * (2 + cos(v)) * ((2 + cos(v + PI * 2 / 3))*0.25))
    return Vec3D.new(x, y, z) * scale if mesh_distortion == 0
    Vec3D.new(x, y, z) * scale + Vec3D.random * mesh_distortion
  end
end
{% endhighlight %}

[generative]:http://www.generative-gestaltung.de
[code]:http://www.generative-gestaltung.de/code
[twitter]:https://twitter.com/monkstoneT
[sketches]:https://github.com/ruby-processing/Generative-Design-Code-in-Ruby
