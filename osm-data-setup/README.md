# OSM Data Setup

## 1. Downloading OSM `.pbf` data

The latest and historical `.pbf` extracts for **Provence-Alpes-Côte d’Azur (PACA)** are available from [Geofabrik](https://download.geofabrik.de/europe/france/provence-alpes-cote-d-azur.html).  
The `.pbf` files can be used for routing, geospatial analysis, or comparison across different years (e.g., 2016 vs. 2025).

```bash
curl -O https://download.geofabrik.de/europe/france/provence-alpes-cote-d-azur-latest.osm.pbf
```

Older snapshots (2016, 2018, etc.) are available in the Geofabrik archive at the same URL.

## 2. Downloading the `084` boundary (GeoJSON)

We also need the administrative boundary of the 084 to clip or filter OSM data.

Then, run the following in your terminal (inside the project directory):

```bash
curl -sG 'https://overpass-api.de/api/interpreter' \
  --data-urlencode 'data=[out:json][timeout:60];
rel["boundary"="administrative"]["admin_level"="6"]["name"="Vaucluse"];
out body; >; out skel qt;' \
| npx osmtogeojson --format=geojson --no-precision-reduction \
> c084.geojson
```

### Create the `.pbf` for `084`

Install `osmium-tool` and run this command:
```bash
osmium extract   --polygon=c084.geojson   --output=c084.osm.pbf --overwrite   paca-250917.pbf 
```

This command creates a new `.pbf` file containing only the `084` data.

