---
layout: page
title: Geographic Projections
---

# {{ page.title }}

Positions on the surface of the Earth are described by two angles, _longitude_ and _latitude_. The longitude of a point on the surface of Earth is the angle between that point, the axis of Earth and an arbitrary reference line known as the  [Prime meridian (Greenwich)](https://en.wikipedia.org/wiki/Prime_meridian_(Greenwich)), the latitude is the angle between the point, the center of Earth and the [Equator](https://en.wikipedia.org/wiki/Equator).

![Longitude and Latitude]({{ site.baseurl }}/img/long-lat.png)

To be able to represent shapes on the surface of Earth on a plane, we need a systematic way associate geographic coordinates _longitude, latitude_ to coordinates in a plane. A function that does this is called a [cartographic projection](https://en.wikipedia.org/wiki/Map_projection).

D3 comes with a set of configurable [geographic projections](https://github.com/mbostock/d3/wiki/Geo-Projections), less commonly used projections are available in the [extended geographic projections plugin](https://github.com/d3/d3-geo-projection) and the [polyhedral projection plugin](https://github.com/d3/d3-plugins/tree/master/geo/polyhedron).

![D3 Projections]({{ site.baseurl }}/img/d3-projections.png)
