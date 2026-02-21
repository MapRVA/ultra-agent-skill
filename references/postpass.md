# Postpass Reference

Postpass is a SQL-based query interface for OpenStreetMap data, exposing a PostGIS-enabled
PostgreSQL database via the [Postpass API](https://github.com/woodpeck/postpass).

Ultra supports Postpass as a query provider via `type: postpass`.

## API Endpoint

The default endpoint is: `https://postpass.geofabrik.de/api/0.2/interpreter`

The full schema is documented at: https://github.com/woodpeck/postpass-ops/blob/main/SCHEMA.md

## Database Schema

The database uses the `osm2pgsql flex` schema with three main geometry tables:

| Table | Geometry Type |
|-------|---------------|
| `postpass_point` | Point |
| `postpass_line` | MultiLineString |
| `postpass_polygon` | MultiPolygon |

Combined geometry views are also available:

- `postpass_pointpolygon`
- `postpass_pointline`
- `postpass_linepolygon`
- `postpass_pointlinepolygon`

### Columns

- **`geom`** — geometry column (PostGIS)
- **`tags`** — JSONB column containing OSM tags

Access tags with:
- `tags->>'key'` — retrieve a tag value as text
- `tags ? 'key'` — check if a tag exists

## Bounding Box Filtering

Use PostGIS functions with Ultra's bbox template variables. The `&&` operator with
`ST_SetSRID(ST_MakeBox2D(...), 4326)` is the standard pattern:

```sql
SELECT tags->>'name' AS name, geom
FROM postpass_point
WHERE tags->>'amenity' = 'cafe'
  AND geom && ST_SetSRID(ST_MakeBox2D(
    ST_MakePoint({{w}}, {{s}}),
    ST_MakePoint({{e}}, {{n}})
  ), 4326)
```

## Basic Usage in Ultra

```yaml
---
type: postpass
style:
  layers:
    - type: circle
      circle-color: '#6F4E37'
      circle-radius: 6
---
SELECT tags->>'name' AS name, geom
FROM postpass_point
WHERE tags->>'amenity' = 'cafe'
  AND geom && ST_SetSRID(ST_MakeBox2D(
    ST_MakePoint({{w}}, {{s}}),
    ST_MakePoint({{e}}, {{n}})
  ), 4326)
```

## Spatial Joins

Use `ST_Contains` for queries involving containment (e.g., features within an admin boundary):

```sql
SELECT point.tags->>'name' AS name, point.geom
FROM postpass_point AS point
JOIN postpass_polygon AS admin
  ON ST_Contains(admin.geom, point.geom)
WHERE point.tags->>'natural' = 'tree'
  AND admin.tags->>'name' = 'Berlin'
  AND admin.tags->>'boundary' = 'administrative'
  AND admin.tags->>'admin_level' = '4'
```

## Line and Polygon Data

For ways (lines) or polygons, use the appropriate table and `type: line` or `type: fill`:

```yaml
---
type: postpass
style:
  layers:
    - type: line
      line-color: '#E88D2A'
      line-width: 3
---
SELECT tags->>'name' AS name, geom
FROM postpass_line
WHERE tags->>'highway' = 'cycleway'
  AND geom && ST_SetSRID(ST_MakeBox2D(
    ST_MakePoint({{w}}, {{s}}),
    ST_MakePoint({{e}}, {{n}})
  ), 4326)
```

## Important Notes

- **No trailing semicolon** — do not end your SQL query with `;`
- **Always include `geom`** in your SELECT when you want geometry rendered on the map
- **Use the correct table** for the geometry type you need (point/line/polygon or combined views)
- PostgreSQL/PostGIS SQL syntax applies
- Results are returned as GeoJSON by default

## Postpass Project Links

- GitHub: https://github.com/woodpeck/postpass
- Operations/Schema: https://github.com/woodpeck/postpass-ops
