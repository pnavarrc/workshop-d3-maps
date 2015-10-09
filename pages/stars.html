---
layout: empty
title: Stars
---

<style>
html, body {
  height: 100%;
}
.stars {
  height: 100%;
}

.graticule {
  fill: none;
  stroke: #B9D5F9;
  stroke-opacity: 0.3;
  stroke-width: 1px;
  pointer-events: none;
}

.sphere {
  fill: #071F3C;
  pointer-events: none;
}

.star {
  fill: #fff;
}

.star-label {
  text-transform: uppercase;
  font-size: 9px;
  fill: #fff;
  fill-opacity: 0.6;
}
</style>

<div class="stars">
  <svg>
    <defs>
     <filter id="blurFilter">
       <feGaussianBlur stdDeviation="0.9"/>
     </filter>
   </defs>
  </svg>
</div>

<script>
d3.json('{{ site.baseurl }}/data/hyg.json', function(error, data) {

  if (error) { console.error(error); }

  // Create the container div
  var div = d3.select('.stars');

  // Compute the width and height of the container div
  var width = parseInt(div.style('width'), 10),
      height = parseInt(div.style('height'), 10);

  // Creates the SVG container and set it's dimensions
  var svg = div.selectAll('svg').data([0]);

  svg.enter().append('svg');

  svg
    .attr('width', width)
    .attr('height', height);

  svg.exit().remove();

  // Stores the projection rotation angles
  var rotate = {x: 0, y: 0};

  // Configure the projection
  var projection = d3.geo.stereographic()
      .scale(4.5 * height / Math.PI)
      .translate([width / 2, height / 2])
      .clipAngle(120)
      .rotate([rotate.x, -rotate.y]);

  // Create and configure the geographic path generator
  var pathGenerator = d3.geo.path()
    .projection(projection);

  // Add the globe outline
  svg.append('path').datum({type: 'Sphere'})
      .attr('class', 'sphere')
      .attr('d', pathGenerator);

  // Creates and draw graticule lines
  var graticule = d3.geo.graticule();

  var lines = svg.selectAll('path.graticule').data([graticule()])
    .enter().append('path')
    .classed('graticule', true);

  lines
    .attr('d', pathGenerator);

  var rScale = d3.scale.linear()
    .domain(d3.extent(data.features, function(d) { return d.properties.mag; }))
    .range([4, 1]);

  // Compute the radius for the point features
  pathGenerator.pointRadius(function(d) {
    return d.properties ? rScale(d.properties.mag) : 1;
  });

  // Computes a color scale that approximates the color of the stars
  var cScale = d3.scale.linear()
    .domain([-0.3, 0, 0.6, 0.8, 1.42])
    .range(['#6495ed', '#fff', '#fcff6c', '#ffb439', '#ff4039']);

  // Add the star features to the SVG container
  var stars = svg.selectAll('path.star-color').data(data.features);

  stars.enter().append('path')
    .classed('star-color', true);

  stars
    .attr('d', pathGenerator)
    .attr('fill', function(d) {
      var color = cScale(d.properties.color);
      return d3.lab(color).brighter(3);
    })
    .attr('filter', 'url(#blurFilter)');

  stars.exit().remove();

  // Add labels for the stars
  var name = svg.selectAll('text').data(data.features);

  name.enter().append('text')
    .classed('star-label', true);

  name
    .attr('x', function(d) { return projection(d.geometry.coordinates)[0] + 8; })
    .attr('y', function(d) { return projection(d.geometry.coordinates)[1] + 8; })
    .text(function(d) { return d.properties.name; });

  name.exit().remove();


  var long = d3.scale.linear()
      .domain([0, width])
      .range([-180, 180]);

  var lat = d3.scale.linear()
      .domain([0, height])
      .range([90, -90]);

  var zoomBehavior = d3.behavior.zoom()
    .translate(projection.translate())
    .scale(projection.scale())
    .on('zoom', function() {

      var t = d3.event.translate;

      projection.rotate([long(t[0]), lat(t[1])]);

      svg.selectAll('path')
        .attr('d', pathGenerator);

      name
        .attr('x', function(d) { return projection(d.geometry.coordinates)[0] + 8; })
        .attr('y', function(d) { return projection(d.geometry.coordinates)[1] + 8; });

    });

  svg.call(zoomBehavior);

});

</script>
