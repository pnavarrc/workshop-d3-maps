---
layout: page
title: Getting Geographic Data
---


# {{page.title}}

One of the most difficult parts of creating maps is to get geographic data files. Most governments will have some geographic data available somewhere, but the quality, file format, scale, features or metadata might not meet our needs.

Sadly, creating a map starts with the boring task of downloading geographic data files, converting them to the formats we need, maybe lowering their resolution and combining them with other geographic datasets. In this section, we will briefly discuss how to download and transform data files, and spend a bit more time discussing how the process works.


## Getting Data

The most reliable source of geographic data that I know of is [Natural Earth](http://www.naturalearthdata.com/). It’s a curated collection of physical, cultural and raster geographic files with homogeneous quality and sensible defaults. The files are in [ESRI Shapefile](https://en.wikipedia.org/wiki/Shapefile) format, which is one of the de facto standards in the industry. A complete set in this format consist of at least 3 files, a `.shp`, a `.shx` and a `.dbf` file.

These files are in binary format and can’t be directly used with D3, so we will need to use some command-line tools to convert them to either GeoJSON or TopoJSON formats.

Learn more:
- [Natural Earth](http://www.naturalearthdata.com)
- [Natural Earth 1:110m Physical Vectors](http://www.naturalearthdata.com/downloads/110m-physical-vectors/)

Let’s download the [land shapefiles](http://www.naturalearthdata.com/http//www.naturalearthdata.com/download/110m/physical/ne_110m_land.zip), which contains land polygons including major islands.

## Converting the Files

To convert the files from one format to another, we will need to install the [Geospatial Data Abstraction Library (GDAL)](http://www.gdal.org/). GDAL provides command-line tools to manipulate and convert geographic data between different formats, shapefiles and GeoJSON among them.

To convert the shapefiles from shapefile format to GeoJSON format, we can use the [ogr2ogr](http://www.gdal.org/ogr_utilities.html) tool


```
$ ogr2ogr -f GeoJSON countries.geojson ne_50m_admin_0_countries.shp
```

This will generate a GeoJSON file that will contain the features and metadata of the original shapefile.


## GeoJSON

GeoJSON is a format to encode geographic information, it describes geographic features in terms of their geometry. GeoJSON supports several geometry types, `Point`, `LineString`, `Polygon`, `MultiPoint`, `MultiLineString`, `MultiPolygon` and `GeometryCollection`. These geometries allow to describe cities, places, countries, lakes and more.

```js
{
  "type": "Feature",
  "properties": {
    "scalerank": 3,
    "featurecla": "Admin-0 country",
    "labelrank": 5.0,
    "sovereignt": "Netherlands",
    "admin": "Aruba",
    "continent": "North America",
    "region_un": "Americas",
    "subregion": "Caribbean",
    // ...
  },
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [ -69.899121093749997, 12.452001953124991 ],
        [ -69.895703125,       12.422998046874994 ],
        [ -69.942187499999989, 12.438525390624989 ],
        [ -70.004150390625,    12.50048828125 ],
        [ -70.066113281249997, 12.546972656249991 ],
        [ -70.050878906249991, 12.597070312499994 ],
        [ -70.035107421874997, 12.614111328124991 ],
        [ -69.97314453125,     12.567626953125 ],
        [ -69.911816406249997, 12.48046875 ],
        [ -69.899121093749997, 12.452001953124991 ]
      ]
    ]
  }
}
```

Of course, Aruba is a small island, so its geometry is really simple. Countries with more complex geography will be described as GeometryCollection objects, so they can describe islands, lakes and unconnected regions.

This description allows us to draw countries and places with as much details as the file contains, and most times is all we need. But there are some shortcomings with this way to describe geography:

- It doesn’t account for relationships between places.
- It’s quite redundant, two countries sharing a frontier will basically duplicate the points in the boundary.
- The paths that describe the frontier might not coincide in some points, allowing for artifacts.
- We will need to project the same points several times as result of this duplication.
- GeoJSON files can be quite big because of this redundancy.

The TopoJSON format aims to solve some of this shortcomings.


## TopoJSON

TopoJSON can refer to three different things:

1. The file format (and its specification)
2. The command line tool
3. the JavaScript library

In this section, we will discuss the file format and the command-line tool. The TopoJSON file format intends to represent both geometric and topologic information of geographic features. It describes geometries as a set of arcs, which can be shared by one or more features.

```js
{
  "type": "Topology",
  "transform": {
    "scale": [0.036003600360036005, 0.017361589674592462],
    "translate": [-180, -89.99892578124998]
  },
  "objects": {
    "aruba": {
      "type": "Polygon",
      "arcs": [[0]],
      "id": 533
    }
  },
  "arcs": [
    [
      [3058, 5901],
      [0, -2],
      [-2, 1],
      [-1, 3],
      [-2, 3],
      [0, 3],
      [1, 1],
      [1, -3],
      [2, -5],
      [1, -1]
    ]
  ]
}
```

Again, the shape of Aruba is simple and consists of just one arc, but more complex countries or geographic features will be composed of several arcs. The `arcs` attribute of the object contains a list of references to the arcs defined below.

The arcs don’t contain geographic coordinates (at least not directly), they contain numbers that can be used to reconstruct geographic coordinates, the idea is to save space and avoid repetition.

As features in TopoJSON files share arcs, we can tell if two features are neighbors, because if they are, they will share at least an arc on their common boundary.

### The topojson command-line tool

In most cases, the TopoJSON file will be generated starting from a geometric format (let’s say GeoJSON), so the topologic information will need to be infered from the geometry.

The process to infer topology is composed of several stages

- Extract lines and rings
- Identify junctions (join points)
- Cutting lines
- Dedup

TopoJSON needs to keep track of which arcs are related to each geometry on the original object. These operations are a bit complex and their specific behavior can be controlled by passing options to the command-line tool

#### Quantization

Quantization is the process of reducing the precision of the geographic coordinates. The original GeoJSON file might have coordinates like `-161.127601284814773, -79.634208673011301`, and we might not need to keep all of them.

Removing decimal places is equivalent to move the points so they belong to a grid, the step size of this grid is the quantization level. If we use `--quantization 1e-6` means we are splitting longitude and latitude on angles of 0.000001 degrees, and moving each point to their closest point in this grid.

The quantization process might create duplicated points (`1.660001` and `1.660004` could be both simplified as `1.6600`), the duplicated points will be removed in this phase. Snapping points to a grid will also make easier to construct topology.

#### Simplification

Simplification is the process of removing points that have less impact in the resulting shape. Simplification is done after the topology is infered to preserve this information. The process involves removing the points that will have less impact in the resulting shape.

#### Other Options

Among other options, the topojson tool allows to configure and apply D3 projections directly during the conversion process, so the application only needs to care about rendering the already projected points. Refer to the [command-line documentation](https://github.com/mbostock/topojson/wiki/Command-Line-Reference) for the complete set of options.


Learn more:
- [Stack Overflow: Quantization vs Simplification](http://stackoverflow.com/questions/18900022/topojson-quantization-vs-simplification)
- [How to Infer Topology](http://bost.ocks.org/mike/topology/)
