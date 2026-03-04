## Multiple Sources

You can add extra sources via `style.sources`. The query body provides one source
(the default `ultra` source), and `style.sources` can add more. Layers target extra
sources with an explicit `source` key.

```
---
type: vector
style:
  version: 8
  sources:
    hillshade:
      type: raster
      url: pmtiles://https://example.com/hillshade.pmtiles
      tileSize: 512
  layers:
    # Layer from the extra source
    - type: raster
      source: hillshade
      paint:
        raster-opacity: 0.5
    # Layer from the query body (default source, no `source` key needed)
    - type: fill
      source-layer: landcover
      fill-color: green
      fill-opacity: 0.6
---
pmtiles://https://example.com/landcover.pmtiles
```

This pattern works for any combination: two PMTiles, PMTiles + raster URL tiles,
PMTiles + Overpass query results, etc.

## Combining PMTiles with Overpass

You can use a PMTiles tileset as a basemap/background and overlay Overpass query results:

```
---
style:
  version: 8
  sources:
    basemap:
      type: vector
      url: pmtiles://https://example.com/basemap.pmtiles
  layers:
    # Basemap layers (from PMTiles)
    - type: fill
      source: basemap
      source-layer: landuse
      fill-color: '#e8e8e8'
    - type: line
      source: basemap
      source-layer: roads
      line-color: '#ccc'
    # Overpass result layers (default source, no `source` key)
    - type: circle
      circle-color: red
      circle-radius: 5
---
[bbox:{{bbox}}];
node[amenity=cafe];
out center;
```

## Reference Examples

- **Overture Landcover + Hillshade** (multi-source): https://overpass-ultra.us/docs/Examples/overture-landcover-plus-hillshade/
- **Overture + OSM POI** (PMTiles + Overpass): https://overpass-ultra.us/docs/Examples/overture-places-and-osm/
