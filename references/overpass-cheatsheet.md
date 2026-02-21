# Overpass QL Cheatsheet

Quick reference for common Overpass QL patterns used in Ultra queries.

Full documentation: https://wiki.openstreetmap.org/wiki/Overpass_API/Overpass_QL

## Query Structure

```
[bbox:{{bbox}}];        // settings (bbox, timeout, etc.)
<statements>;           // query statements
out <mode>;             // output
```

## Element Types

| Keyword | Meaning |
|---------|---------|
| `node`  | Point features |
| `way`   | Lines and polygon outlines |
| `relation` | Collections of elements (routes, boundaries, etc.) |
| `nwr`   | Shorthand for node + way + relation |

## Bounding Box

```
// Dynamic (current map viewport in Ultra):
[bbox:{{bbox}}];

// Fixed:
[bbox:south,west,north,east];

// On a single statement:
node[amenity=cafe](48.8,2.2,48.9,2.4);
```

## Tag Filters

```
nwr[key=value]          // exact match
nwr[key!=value]         // not equal
nwr[key]                // key exists
nwr[!key]               // key doesn't exist
nwr[key~"regex"]        // regex match
nwr[key!~"regex"]       // regex doesn't match
nwr["key"="value"]      // quoted form (required for special chars like colons)
```

## Multiple Filters (AND)

Chain filters — all must match:

```
node[amenity=restaurant][cuisine=pizza];
```

## Union (OR)

Wrap in parentheses:

```
(
  node[amenity=cafe];
  node[amenity=restaurant];
);
```

## Area Filters

Query within a named area (city, country, etc.):

```
area["name"="Berlin"]->.a;
node[amenity=pub](area.a);
out;
```

Or use `{{geocodeArea:Name}}` shorthand:

```
node[amenity=pub](area:{{geocodeArea:Berlin}});
out;
```

Note: `{{geocodeArea:...}}` is an Overpass Turbo/Ultra shortcut, not core Overpass QL.

## Recurse / Around

```
// All nodes belonging to ways in the result set:
way[highway=primary];
(._;>;);
out;

// Elements within N meters of a point:
node(around:1000,48.856,2.352)[amenity=cafe];
out;
```

## Output Modes

| Mode | Description | When to use |
|------|-------------|-------------|
| `out;` | Tags + coords for nodes | Simple node queries |
| `out center;` | Tags + centroid for ways/relations | Point visualization of areas |
| `out geom;` | Tags + full geometry | Line/polygon visualization |
| `out body;` | Tags, no coords | Rarely used alone |
| `out skel qt;` | Minimal output, sorted | After `>;` recurse |
| `out meta;` | Includes metadata (user, timestamp) | When you need edit history |
| `out count;` | Just counts elements | Statistics |

Modifiers can be combined: `out center qt 100;` (centered, sorted, limit 100).

## Timeout and Maxsize

```
[timeout:60][maxsize:100000000];
```

Default timeout is 25 seconds. Increase for large/complex queries.

## Common OSM Tags

### Points of Interest
- `amenity=` cafe, restaurant, bar, pub, bank, atm, pharmacy, hospital, school,
  library, parking, fuel, bicycle_parking, bicycle_repair_station, drinking_water,
  toilets, bench, waste_basket, vending_machine, post_box
- `shop=` supermarket, convenience, bakery, bicycle, clothes, electronics, hairdresser
- `tourism=` hotel, motel, hostel, museum, artwork, viewpoint, information, camp_site
- `leisure=` park, playground, pitch, swimming_pool, garden, nature_reserve

### Transportation
- `highway=` motorway, trunk, primary, secondary, tertiary, residential, service,
  footway, cycleway, path, bus_stop, crossing
- `railway=` station, halt, tram_stop, subway_entrance
- `public_transport=` stop_position, platform, station
- `cycleway=` lane, track, shared_lane

### Infrastructure
- `building=` yes, residential, commercial, industrial, church, school
- `landuse=` residential, commercial, industrial, farmland, forest, meadow
- `natural=` tree, water, wood, grassland, peak, cliff
- `waterway=` river, stream, canal, ditch

### Metadata
- `name=` feature name
- `addr:street=`, `addr:housenumber=` address components
- `opening_hours=` business hours
- `wheelchair=` yes, no, limited
- `wikidata=` Wikidata Q-ID
