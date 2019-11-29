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

# Interaction in nub

Jean Pierre Charalambos

H:

# Index

 1. Goal <!-- .element: class="fragment" data-fragment-index="1"-->
 2. Nub design<!-- .element: class="fragment" data-fragment-index="2"-->
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

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design

1. API considerations
2. The eye
3. Picking & Manipulation
4. Application Control

V:

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
### API considerations

> The _scene_ is a high-level [Processing](https://processing.org/) scene-graph handler

> A _node_ encapsulates a 2D/3D coordinate system

Simplicity: A _scene_ and some _nodes_ attached to it implement the _3MITs_

V:

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
### The scene

> High-level scene-graph object which provides eye, input and timing handling to [Processing](https://processing.org/)

The _scene_ also implements some drawing routines not present in [Processing](https://processing.org/)

V:

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
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
The scene _eye_ is just a [Node](https://visualcomputing.github.io/nub-javadocs/nub/core/Node.html) instance

V:

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
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
Node n1, n2, n3;
// to be run only at start up:
void setup() {
  Scene scene = new Scene();
  // creates a hierarchy of 'attached-nodes'
  n1 = new Node(scene);
  n2 = new Node(n1);
  n3 = new Node(n1) {
    // note that within graphics the geometry is defined
    // at the node local coordinate system
    @Override
    public boolean graphics(PGraphics pg) {
      pg.sphere(50);
    }
  };
}
```
Override the [Node](https://visualcomputing.github.io/nub-javadocs/nub/core/Node.html) _graphics(PGraphics)_ method to customize the node appearance.

V:

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
### [Hierarchical rendering](https://github.com/VisualComputing/nub#rendering)

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

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
### Picking & Interaction

> 1. Picking -> Tag a _node_ using an arbitrary name

> 2. Interaction -> Converts user gesture data into a _node_ interaction

V:

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
### Picking & Interaction

> [tag(tag, node)](https://visualcomputing.github.io/nub-javadocs/nub/core/Graph.html#tag-java.lang.String-nub.core.Node-)

<li class="fragment"> Low-level ray casting (yes/no): [tracks(node, pixelX, pixelY)](https://visualcomputing.github.io/nub-javadocs/nub/core/Graph.html#tracks-nub.core.Node-int-int-)
<li class="fragment"> (Optimized) High-level ray casting (updates the tagged-node): [tag(hid, pixelX, pixelY)](https://visualcomputing.github.io/nub-javadocs/nub/core/Graph.html#tag-java.lang.String-int-int-)

V:

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
### Picking & Interaction

> Eye interaction pattern: ```interactEye(gesture...)```

> Node interaction pattern: ```interactNode(node, gesture...)```

> Tagged node interaction pattern: ```interactTag(tag, gesture...) : interactNode(node(tag), gesture...)```

> Tagged node or eye interaction pattern: ```interact(tag, gesture...) : if (!interactTag(tag, gesture...)) interactEye(gesture...)```

<li class="fragment"> ```gesture...``` is a [varargs](https://docs.oracle.com/javase/8/docs/technotes/guides/language/varargs.html) argument defining the [screen-space](https://github.com/VisualComputing/nub#space-transformations) user gesture.
<li class="fragment"> [node(tag)](https://visualcomputing.github.io/nub-javadocs/nub/core/Graph.html#node-java.lang.String-) returns the node tagged with ```tag```, which may be ```null```.
<li class="fragment"> Details and code snippets [here](https://github.com/VisualComputing/nub#interactivity).

V:

## [Nub](https://visualcomputing.github.io/nub-javadocs/) Design
### Picking & Interaction: Custom node behaviors

Override the node [interact(gesture...)](https://visualcomputing.github.io/nub-javadocs/nub/core/Node.html#interact-java.lang.Object...-) method and then invoke it with the _node interaction_ or _tagged node interaction_ patterns above.

<li class="fragment"> Check the [CustomNodeInteraction](https://github.com/VisualComputing/nub/blob/master/examples/demos/CustomNodeInteraction/CustomNodeInteraction.pde) example.
<li class="fragment"> To customize the eye behavior, refer to the [CustomEyeInteraction](https://github.com/VisualComputing/nub/tree/master/examples/demos/CustomEyeInteraction) example.

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

> All [examples](https://github.com/VisualComputing/nubjs/tree/processing/examples) using a mouse and/or a keyboard

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
