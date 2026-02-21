# QLever Reference

QLever is a SPARQL engine optimized for large knowledge graphs, including the full
OpenStreetMap planet. Ultra supports QLever via `type: qlever`.

## Before Writing a QLever Query

QLever uses SPARQL syntax against OSM data. The query patterns differ significantly from
Overpass QL. Before writing a QLever query, **fetch and study the example queries**:

https://wiki.openstreetmap.org/wiki/QLever/Example_queries

These examples cover common patterns like filtering by tag, spatial queries, and
combining OSM data with Wikidata.

## Basic Usage in Ultra

```yaml
---
type: qlever
style:
  layers:
    - type: circle
      circle-color: green
---
PREFIX osm: <https://www.openstreetmap.org/>
PREFIX osmkey: <https://www.openstreetmap.org/wiki/Key:>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>

SELECT ?osm_id ?geo ?name WHERE {
  ?osm_id osmkey:amenity "cafe" ;
          geo:hasGeometry/geo:asWKT ?geo ;
          osm:name ?name .
}
LIMIT 1000
```

## Key Things to Know

- QLever uses SPARQL, not SQL or Overpass QL
- The default backend is `osm-planet` (full OSM dataset)
- Override with `server: <backend-slug>` in the YAML frontmatter
- Results must include a geometry column (typically `?geo`) for Ultra to render them
- QLever can join OSM data with Wikidata for enriched queries
- Performance is generally fast even for planet-scale queries
- For spatial filtering, you have two options:
  - **Relation-based**: Use `ogc:sfContains` with an OSM relation ID (e.g.,
    `osmrel:5396194 ogc:sfContains ?element .` for Washington, D.C.)
  - **Viewport-based**: Ultra replaces `{{s}}`, `{{n}}`, `{{e}}`, `{{w}}` with the current
    map viewport coordinates before sending the query. You can use these in a SPARQL
    bounding box filter like:
    ```sparql
    BIND("POLYGON(({{w}} {{s}}, {{e}} {{s}}, {{e}} {{n}}, {{w}} {{n}}, {{w}} {{s}}))"^^geo:wktLiteral AS ?bbox)
    FILTER(geof:sfWithin(?geo, ?bbox))
    ```
    If this spatial filter doesn't work with the QLever backend, fall back to
    relation-based filtering or use `LIMIT` and let the user pan to their area.
    Consult the Ultra QLever example for a known-working pattern:
    https://overpass-ultra.us/docs/Examples/qlever/

## QLever Resources

- Example queries: https://wiki.openstreetmap.org/wiki/QLever/Example_queries
- QLever UI: https://qlever.cs.uni-freiburg.de/
- OSM wiki page: https://wiki.openstreetmap.org/wiki/QLever
