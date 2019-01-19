<section id="themes">
	<h2>Themes</h2>
		<p>
			Set your presentation theme: <br>
			<!-- Hacks to swap themes after the page has loaded. Not flexible and only intended for the reveal.js demo deck. -->
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/black.css'); return false;">Black (default)</a> -
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/white.css'); return false;">White</a> -
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/league.css'); return false;">League</a> -
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/sky.css'); return false;">Sky</a> -
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/beige.css'); return false;">Beige</a> -
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/simple.css'); return false;">Simple</a> <br>
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/serif.css'); return false;">Serif</a> -
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/night.css'); return false;">Night</a> -
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/moon.css'); return false;">Moon</a> -
			<a href="#" onclick="document.getElementById('theme').setAttribute('href','css/theme/solarized.css'); return false;">Solarized</a>
		</p>
</section>

H:

# Interaction in [frames](https://github.com/VisualComputing/frames)

Jean Pierre Charalambos

H:

# Index

 1. Goal <!-- .element: class="fragment" data-fragment-index="1"-->
 2. Frames' design<!-- .element: class="fragment" data-fragment-index="2"-->
 3. Applications<!-- .element: class="fragment" data-fragment-index="3"-->
 4. Future work<!-- .element: class="fragment" data-fragment-index="4"-->
 
H:

## Goal

Provide _interactivity_ to _application objects_ from any _input source_

in the 'simplest' possible way <!-- .element: class="fragment" data-fragment-index="1"-->

V:

## Goal: Main interaction tasks

Three main interaction tasks (see ['A Survey of Interaction Techniques for Interactive 3D Environments', Jankowski et al](https://hal.inria.fr/hal-00789413/)):

<li class="fragment"> Navigation
<li class="fragment"> Picking and manipulation
<li class="fragment"> Application control

V:

## Goal: Main interaction tasks
### 1. 2D & 3D Navigation

Basic camera types:

<li class="fragment"> Orbit-like methods
<li class="fragment"> First person
<li class="fragment"> Third person

V:

## Goal: Main interaction tasks
### 2. Picking & Manipulation

<li class="fragment"> Picking strategies: from input sources, programmatically
<li class="fragment"> Manipulation: emulate 6 DOF's (Default behavior from multiple _input Sources_)

V:

## Goal: Main interaction tasks
### 3. Application control (custom behaviors)

Post-WIMP interaction styles
<li class="fragment"> Interfaces “containing at least one interaction technique
not dependent on classical 2D widgets” [[van Dam]](http://dl.acm.org/citation.cfm?id=253708), such as:
<li class="fragment"> Virtual, mixed and augmented reality
<li class="fragment"> [Tangible interaction](https://en.wikipedia.org/wiki/Tangible_user_interface), ubiquitous and pervasive computing, context-aware computing
<li class="fragment"> Handheld, or mobile interaction
<li class="fragment"> Perceptual and [affective computing](https://en.wikipedia.org/wiki/Affective_computing)
  
N:

WIMP: "window, icon, menu, pointing device"
classical 2D widgets:  menus and icons

H:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design

1. API considerations
2. The eye
3. Picking & Manipulation
4. Application Control

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### API considerations

> The _scene_ is a high-level [Processing](https://processing.org/) scene-graph handler

> A _frame_ is a 2D/3D coordinate system


Simplicity: A _scene_ and some _frames_ attached to it implement the _3MITs_

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### The scene

> High-level scene-graph object which provides eye, input and timing handling to [Processing](https://processing.org/)

The _scene_ also implements some drawing routines not present in [Processing](https://processing.org/)

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Default eye

```java
 World
  ^
   \
    eye
```

```java
Scene scene;
// to be run only at start up:
void setup() {
  Scene scene = new Scene();
}
```
The scene _eye_ is just a [Frame](https://visualcomputing.github.io/frames-javadocs/frames/core/Frame.html) instance

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Hierarchy setup

```java
 World
  ^
  |\
  1 eye
  ^
  |\
  2 3
```

```java
Scene scene;
Frame f1, f2, f3;
// to be run only at start up:
void setup() {
  Scene scene = new Scene();
  // creates a hierarchy of 'attached-frames'
  f1 = new Frame(scene);
  f2 = new Frame(f1);
  f3 = new Frame(f1) {
    // note that within graphics the geometry is defined
    // at the frame local coordinate system
    @Override
    public boolean graphics(PGraphics pg) {
      pg.sphere(50);
    }
  };
}
```
Override the [Frame](https://visualcomputing.github.io/frames-javadocs/frames/core/Frame.html) _graphics(PGraphics)_ method to customize the frame role

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Hierarchy rendering algorithm

```java
 World
  ^
  |\
  1 eye
  ^
  |\
  2 3
```

```java
// to be run continously
void draw() {
  scene.render();
}
```
<!-- .element: class="fragment" data-fragment-index="1"-->

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Picking & Manipulation

> 1. Picking -> Tag a _frame_ using a [Human Interface Device (hid)](https://en.wikipedia.org/wiki/Human_interface_device) name

> 2. Manipulation -> Physical space-based _frame_ interaction

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Picking & Manipulation

> [scene.setTrackedFrame(hid, frame)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#setTrackedFrame-java.lang.String-frames.core.Frame-)

<li class="fragment"> Low-level ray casting (yes/no): [scene.tracks(pixel, frame)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#tracks-frames.primitives.Point-frames.core.Frame-)
<li class="fragment"> (Optimized) High-level ray casting (updates the hid tracked-frame): [scene.cast(hid, pixel)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#cast-java.lang.String-frames.primitives.Point-)

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Picking & Manipulation

> Frame interaction pattern: ```scene.interact(param1,..., paramN, frame)```

> HID interaction pattern: ```scene.interact(hid, param1,..., paramN) = scene.interact(param1,..., paramN, defaultFrame(hid))```

> Default HID interaction patter: ```scene.interact(param1,..., paramN) = scene.interact(param1,..., paramN, defaultFrame())```

<li class="fragment"> ```param1,..., paramN``` define the user gesture
<li class="fragment"> [scene.defaultFrame(hid)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#defaultFrame-java.lang.String-): ```return trackedFrame(hid) == null ? eye() : trackedFrame(hid)```
<li class="fragment"> Examples: [scene.translate(dx, dy, dz, frame)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#translate-float-float-float-frames.core.Frame-) and [scene.translate(hid, dx, dy, dz)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#translate-java.lang.String-float-float-) or [scene.rotate(roll, pitch, yaw, frame)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#rotate-float-float-float-frames.core.Frame-) and [scene.rotate(hid, roll, pitch, yaw)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#rotate-java.lang.String-float-float-float-).

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Application control

Override [Frame.interact(Object... gesture)](https://visualcomputing.github.io/frames-javadocs/frames/core/Frame.html#interact-java.lang.Object...-)

> Frame interaction pattern: ```scene.control(Frame frame, Object... gesture) = frame.interact(gesture)```

> HID interaction pattern: ```scene.control(String hid, Object... gesture) = interact(scene.defaultFrame(hid), gesture)```

> Default HID interaction pattern 2: ```scene.defaultHIDControl(Object... gesture) = interact(scene.defaultFrame(), gesture)```

<li class="fragment"> ```Object...``` define the user gesture
<li class="fragment"> [scene.defaultFrame(hid)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#defaultFrame-java.lang.String-): ```return trackedFrame(hid) == null ? eye() : trackedFrame(hid)```

H:

## Applications

* Navigation
* Picking and manipulation
* Application control

V:

## Applications
### Navigation
#### Orbit-like methods

> All examples using a mouse

V:

## Applications
### Navigation
#### First & third person

<video controls data-autoplay loop src="vid/flock.ogv"></video>

V:

## Applications
### Picking
#### `FIXED` & `ADAPTIVE` precision

<video controls data-autoplay loop src="vid/cajasorientadas.ogv"></video>

V:

## Applications
### Picking
#### `EXACT` precision

<video controls data-autoplay loop src="vid/dof.ogv"></video>

V:

## Applications
### Manipulation
#### Stadandar HID's

> All [examples](https://github.com/VisualComputing/frames/tree/master/examples) using a mouse and/or a keyboard

V:

## Applications
### Manipulation
#### Non-conventional HID's

<video controls data-autoplay loop src="vid/spacenavigator.ogv"></video>

V:

## Applications
### Application control

<video controls data-autoplay loop src="vid/control.ogv"></video>

H:

## References

* ['A Survey of Interaction Techniques for Interactive 3D Environments', Jankowski et al](https://hal.inria.fr/hal-00789413/)
* [Proscene: A feature-rich framework for interactive environments](https://www.sciencedirect.com/science/article/pii/S235271101730002X?_rdoc=1&_fmt=high&_origin=gateway&_docanchor=&md5=b8429449ccfc9c30159a5f9aeaa92ffb)
