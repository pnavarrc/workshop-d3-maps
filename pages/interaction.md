---
layout: page
title: Adding Interactions
---

# {{ page.title }}

One important part on creating maps with D3 is adding interactive behavior, maybe changing something when the user clicks or hovers over a country, or more complex interactions like zooming and rotating the complete map. In this section, we will discuss some ways to implement this kind of interactions.


## Hovering and Click

One behavior users will expect from map-based visualizations is the ability to click to get more details. We will want to indicate the users that something is clickable by changing the pointer style and highlighting features on hover.

[Block: Hover and Click to Zoom](http://bl.ocks.org/pnavarrc/b7accd7b252c85907a79)

![Click to Zoom]({{ site.baseurl }}/img/click-zoom.png)


## Zooming and Panning

If the shape of the path doesn’t change when zooming, it will be easier to just apply a transformation (translate and scale) a group containing the path. This will just zoom and translate the shapes though, and the strokes will be too wide. If there are also labels on the map, the labels will also look weird.

One solution is to change the font-size and stroke-width depending on the zoom level. This also allows to show more labels and details as we zoom in, and to hide some details as we zoom out.

[Block: Zooming and Panning (transform)](http://bl.ocks.org/pnavarrc/d317fcf045598e38f2b0)

![Zoom by Transform]({{ site.baseurl }}/img/zoom-behavior.png)

When the projection changes the shapes on changes of scale or position, we will need to recompute the paths as we zoom or pan the map. Depending on the projection and the level of detail of the GeoJSON features, recomputing (and rendering) the projection and the path could be an expensive operation.

[Block: Zooming and Panning (recomputing)](http://bl.ocks.org/pnavarrc/582ef07fe7974ef59deb)

![Block: Zooming and Rotating]({{ site.baseurl }}/img/map-reprojecting.png)

Learn more:

- [Improved rotation by Jason Davies](https://www.jasondavies.com/maps/rotate/)

## Using Canvas

 If we don’t need the DOM (to bind events, for instance), it might be a good idea to use [canvas](http://diveintohtml5.info/canvas.html) instead to draw the map. Fortunately, the `d3.geo.path` method allows to draw on canvas as well as on SVG.

[Block: Using Canvas](http://bl.ocks.org/pnavarrc/43dd75fb3693588c99ff)

<img class="image-60" src="{{ site.baseurl }}/img/map-canvas.png" alt="Using Canvas"/>
