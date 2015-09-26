---
layout: page
title: Geographic Projections
---

# {{ page.title }}

Positions on the surface of the Earth are described by two angles, _longitude_ and _latitude_. The longitude of a point on the surface of Earth is the angle between that point, the axis of Earth and an arbitrary reference line known as the  [Prime meridian (Greenwich)](https://en.wikipedia.org/wiki/Prime_meridian_(Greenwich)), the latitude is the angle between the point, the center of Earth and the [Equator](https://en.wikipedia.org/wiki/Equator).

<img class="image-60" src="{{ site.baseurl }}/img/long-lat.png" alt="Longitude and latitude"/>

To be able to represent shapes on the surface of Earth on a plane, we need a systematic way associate geographic coordinates _longitude, latitude_ to coordinates in a plane. A function that does this is called a [cartographic projection](https://en.wikipedia.org/wiki/Map_projection).

D3 comes with a set of configurable [geographic projections](https://github.com/mbostock/d3/wiki/Geo-Projections), less commonly used projections are available in the [extended geographic projections plugin](https://github.com/d3/d3-geo-projection) and the [polyhedral projection plugin](https://github.com/d3/d3-plugins/tree/master/geo/polyhedron).

<img class="image-80" src="{{ site.baseurl }}/img/d3-projections.png" alt="D3 Projections"/>


### Equirectangular

[Block: Equirectangular Projection](http://bl.ocks.org/pnavarrc/7dbc309801d07610de44)

```js
// https://github.com/mbostock/d3/blob/master/src/geo/equirectangular.js
function d3_geo_equirectangular(λ, φ) {
  return [λ, φ];
}
```

<img src="{{ site.baseurl }}/img/equirectangular.png" alt="Equirectangular"/>


### Azimutal Equal Area

[Block: Azimutal Equal Area](http://bl.ocks.org/pnavarrc/278fa3086de22d1e2807)

```js
// Abstract azimuthal projection.
// https://github.com/mbostock/d3/blob/master/src/geo/azimuthal.js
function d3_geo_azimuthal(scale, angle) {
  function azimuthal(λ, φ) {
    var cosλ = Math.cos(λ),
        cosφ = Math.cos(φ),
        k = scale(cosλ * cosφ);
    return [
      k * cosφ * Math.sin(λ),
      k * Math.sin(φ)
    ];
  }

  azimuthal.invert = function(x, y) {
    var ρ = Math.sqrt(x * x + y * y),
        c = angle(ρ),
        sinc = Math.sin(c),
        cosc = Math.cos(c);
    return [
      Math.atan2(x * sinc, ρ * cosc),
      Math.asin(ρ && y * sinc / ρ)
    ];
  };

  return azimuthal;
}

// https://github.com/mbostock/d3/blob/master/src/geo/azimuthal-equal-area.js
var d3_geo_azimuthalEqualArea = d3_geo_azimuthal(
  function(cosλcosφ) { return Math.sqrt(2 / (1 + cosλcosφ)); },
  function(ρ) { return 2 * Math.asin(ρ / 2); }
);
```

<img class="image-60" src="{{ site.baseurl }}/img/azimutal-equal-area.png" alt="Equirectangular"/>


### Mercator

[Block: Mercator](http://bl.ocks.org/pnavarrc/85bccaa9a9c85f8fcc06)

```js
function d3_geo_mercator(λ, φ) {
  return [λ, Math.log(Math.tan(π / 4 + φ / 2))];
}
```

<img class="image-60" src="{{ site.baseurl }}/img/mercator.png" alt="Mercator"/>


### Orthographic

[Block: Orthographic](http://bl.ocks.org/pnavarrc/37b9451de2bf03b2ced9)

```js
// https://github.com/mbostock/d3/blob/master/src/geo/orthographic.js
var d3_geo_orthographic = d3_geo_azimuthal(
  function() { return 1; },
  Math.asin
);
```

<img src="{{ site.baseurl }}/img/orthographic.png" alt="Orthographic"/>


### Hammer Projection (D3 Geo Projection Plugin)

[Block: Hammer Projection](http://bl.ocks.org/pnavarrc/d920008a82f67ef2722d)

<img src="{{ site.baseurl }}/img/d3-geo-projection-plugin.png" alt="Hammer"/>

## Projecting Points

Sometimes we won’t use the projection function to draw SVG paths, we can use the projections to project individual locations.


### Using the Location API

[Block: Location API](http://bl.ocks.org/pnavarrc/97a30792badaa68e195a)

![Location API]({{ site.baseurl }}/img/location-api.png)


### Earthquake Map

[Block: Earthquake Map](http://bl.ocks.org/pnavarrc/55e9082ac7a5a344b4ee)

![Earthquakes]({{ site.baseurl }}/img/earthquakes.png)


## Centering and Scaling


As you probably know, projections will distort aspects of Earth, sometimes quite seriously. The Mercator projection, for instance, was created in the 16th century to help navigation, as it preserves lines of constant course and angles with respect to meridians and parallels, it distorts the size of objects near the poles.

For instance, we usually see Greenland as in the image in the left, but in if we rotate the projection on the centroid of Greenland, we get something closer to reality (in relative size, that’s it)

<div class="container">
  <div class="rows cols-image">
    <div class="six columns">
      <img src="{{ site.baseurl }}/img/greenland-default.png" alt="Greenland"/>
      <a href="http://bl.ocks.org/pnavarrc/b77465f09de3fce556b7">Block: Greenland</a>
    </div>
    <div class="six columns">
      <img src="{{ site.baseurl }}/img/greenland-centered.png" alt="Greenland (Centered)"/>
      <a href="http://bl.ocks.org/pnavarrc/0fa582d6eff29799600c">Block: Greenland (Centered)</a>
    </div>
  </div>
</div>

Other than the projections, D3 provides tools to compute attributes of geographic features, most of them are part of the [spherical math](https://github.com/mbostock/d3/wiki/Geo-Paths#spherical-math) module. These functions are useful to compute the centroid, area and bounds of features and use this to center or scale the projections.

![Greenland Scaled]({{ site.baseurl }}/img/greenland-scaled.png)

[Block: Greenland (Scaled and Centered)](http://bl.ocks.org/pnavarrc/fb47d4cab4592e390323)


## Inverse Projection

[Block: Inverse Projection](http://bl.ocks.org/pnavarrc/5bb1a7c2b4c5b624bb83)

![Inverse Projection]({{ site.baseurl }}/img/inverse-projection.png)

## Countries by Size

[Block: Countries by Size](http://bl.ocks.org/pnavarrc/12e05466d99b99dbee85)

![Countries by Size]({{ site.baseurl }}/img/country-sizes.png)

## More Examples

- [Maps with TopoJSON](http://bl.ocks.org/pnavarrc/600f1fd88198e0a727e7)
- [Square Countries](http://bl.ocks.org/pnavarrc/14ed098d4072be2715db)
- [Automatic Label Placement](http://bl.ocks.org/pnavarrc/5913636)
