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

# Interaction in [frames](https://github.com/VisualComputing/framesjs)

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
### API considerations

<li class="fragment"> Simplicity: Separate _application object_ behaviors from _input_
<li class="fragment"> Flexibility: Simple default (common) behaviors vs challenging ones

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Frame notion

> A _frame_ is defined by following affine (composed) transformation: `$T(x,y,z) * R_u(\beta) * S(s)$`, `$s > 0$`

<li class="fragment"> A _frame_ is useful to position, orient and scale objects (including the _eye_) within a virtual environment
<li class="fragment"> The scene may be rendered from any _frame_ point-of-view

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Frame notion: Behavior

```java
frame = new Frame();
pushMatrix();
// behavior
applyTransformation(frame);
// appearance
drawApplicationObject();
popMatrix();
```

```java
applyTransformation(frame) {
  // T(x,y,z)
  translate(frame.translation());
  // Ru(β)
  rotate(frame.rotation());
  // S(s)
  scale(frame.scaling());
}
```
<!-- .element: class="fragment" data-fragment-index="1"-->

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Frame notion: Scene-Graph

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
Frame eye, f1, f2, f3;
eye = new Frame();
f1 = new Frame();
f2 = new Frame(f1);
f3 = new Frame(f1);
```
<!-- .element: class="fragment" data-fragment-index="1"-->

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Frame notion: Scene-Graph

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
// enter f1
pushMatrix();
applyTransformation(f1);
drawApplicationObject1();
// enter f2
pushMatrix();
applyTransformation(f2);
drawApplicationObject2();
// "return" to f1
popMatrix();
// enter f3
pushMatrix();
applyTransformation(f3);
drawApplicationObject3();
// return to f1
popMatrix();
// return to World
popMatrix();
```
<!-- .element: class="fragment" data-fragment-index="1"-->

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Scene class
#### Default eye

> ```scene = new Scene()```

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
Frame eye, f1, f2, f3;
// a default eye is also created
scene = new Scene();
f1 = new Frame(scene);
f2 = new Frame(f1);
f3 = new Frame(f1);
```
<!-- .element: class="fragment" data-fragment-index="1"-->

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Scene class
#### Traversal algorithm

> ```scene.traverse()```

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
scene.traverse();
```
<!-- .element: class="fragment" data-fragment-index="1"-->

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Scene class
#### Input

> 1. Picking -> Tag a _frame_ using a [Human Interface Device (hid)](https://en.wikipedia.org/wiki/Human_interface_device) name

> 2. Manipulation -> Physical space-based _frame_ interaction

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Scene class
#### Input: Picking

> [scene.setTrackedFrame(hid, frame)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#setTrackedFrame-java.lang.String-frames.core.Frame-)

<li class="fragment"> Low-level ray casting (yes/no): [scene.tracks(pixel, frame)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#tracks-frames.primitives.Point-frames.core.Frame-)
<li class="fragment"> (Optimized) High-level ray casting (updates the hid tracked-frame): [scene.cast(hid, pixel)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#cast-java.lang.String-frames.primitives.Point-)

V:

## [Frames](https://visualcomputing.github.io/frames-javadocs/) Design
### Scene class
#### Input: Manipulation

> frame interaction pattern: ```scene.interact(param1,..., paramN, frame)```

> Tagged frame interaction pattern: ```scene.interact(hid, param1,..., paramN) = scene.interact(param1,..., paramN, defaultFrame(hid))```

<li class="fragment"> ```param1,..., paramN``` are physical (screen-space) parameters
<li class="fragment"> [scene.defaultFrame(hid)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#defaultFrame-java.lang.String-): ```return trackedFrame(hid) == null ? eye() : trackedFrame(hid)```
<li class="fragment"> Examples: [scene.translate(dx, dy, dz, frame)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#translate-float-float-float-frames.core.Frame-) and [scene.translate(hid, dx, dy, dz)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#translate-java.lang.String-float-float-) or [scene.rotate(roll, pitch, yaw, frame)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#rotate-float-float-float-frames.core.Frame-) and [scene.rotate(hid, roll, pitch, yaw)](https://visualcomputing.github.io/frames-javadocs/frames/core/Graph.html#rotate-java.lang.String-float-float-float-).

H:

## Applications

* Navigation
* Picking and manipulation
* Application control

V:

## Applications
### Navigation

<li class="fragment"> Orbit-like methods: all examples using a mouse
<li class="fragment"> First person: first person
<li class="fragment"> Third person: [FlockOfBoids](https://github.com/VisualComputing/framesjs/tree/processing/examples/Advanced/FlockOfBoids)

V:

## Applications
### Picking

<li class="fragment"> `FIXED` picking precision, [FlockOfBoids](https://github.com/VisualComputing/framesjs/tree/processing/examples/Advanced/FlockOfBoids)
<li class="fragment"> `ADAPTIVE` picking precision, [CajasOrientadas](https://github.com/VisualComputing/framesjs/tree/processing/examples/Advanced/CajasOrientadas)
<li class="fragment"> `EXACT` picking precision, [PostEffects](https://github.com/VisualComputing/framesjs/tree/processing/examples/Demos/PostEffects)

V:

## Applications
### Manipulation

<li class="fragment"> All examples involving the [mouse]() (default _agent_)
<li class="fragment"> [Space Navigator](https://github.com/VisualComputing/framesjs/tree/processing/examples/Demos/SpaceNavigator) (custom _agent_)
<li class="fragment"> [Kinect 6-DOF cemera control](https://www.youtube.com/watch?v=G8SEzFMmMyI) (custom _agent_)

V:

## Applications

Application control: [deformation](https://github.com/nakednous/Deformation)

H:

## References

* ['A Survey of Interaction Techniques for Interactive 3D Environments', Jankowski et al](https://hal.inria.fr/hal-00789413/)
* [Proscene: A feature-rich framework for interactive environments](https://www.sciencedirect.com/science/article/pii/S235271101730002X?_rdoc=1&_fmt=high&_origin=gateway&_docanchor=&md5=b8429449ccfc9c30159a5f9aeaa92ffb)
