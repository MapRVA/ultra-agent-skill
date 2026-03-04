# PMTiles

## Single PMTiles Source

Set `type: vector` (or `raster`) and use a `pmtiles://` URL as the query body:

```
---
type: vector
options:
  zoom: 12
  center: [11.24, 43.78]
style:
  version: 8
  layers:
    - type: fill
      source-layer: landuse
      fill-color: steelblue
    - type: line
      source-layer: roads
---
pmtiles://https://pmtiles.io/protomaps(vector)ODbL_firenze.pmtiles
```

Key points:
- The `pmtiles://` prefix before the HTTPS URL is required.
- Use `source-layer` to target specific layers within the tileset.
- `version: 8` is required when defining a full style (no `extends`).

## Reference Examples

- **PMTiles basic**: https://overpass-ultra.us/docs/MapLibre-Examples/pmtiles/
