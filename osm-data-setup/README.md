# OSM Data Setup

## 1. Downloading OSM `.pbf` data

The latest and historical `.pbf` extracts for **Provence-Alpes-Côte d’Azur (PACA)** are available from [Geofabrik](https://download.geofabrik.de/europe/france/provence-alpes-cote-d-azur.html).  
The `.pbf` files can be used for routing, geospatial analysis, or comparison across different years (e.g., 2016 vs. 2025).

**Example** (download latest PACA extract and filter Vaucluse later):

```bash
curl -O https://download.geofabrik.de/europe/france/provence-alpes-cote-d-azur-latest.osm.pbf
```

Older snapshots (2016, 2018, etc.) are available in the Geofabrik archive at the same URL.

## 2. Downloading the Vaucluse boundary (GeoJSON)

We also need the administrative boundary of the Vaucluse to clip or filter OSM data.

Then, run the following in your terminal (inside the project directory):

```bash
curl -G 'https://overpass-api.de/api/interpreter'   --data-urlencode 'data=[out:json];rel["boundary"="administrative"]["admin_level"="6"]["name"="Vaucluse"];out geom;' | jq -r '
  {"type":"FeatureCollection",
   "features":[.elements[]
     | select(.type=="relation")
     | {"type":"Feature","properties":{"id":.id},
        "geometry":{"type":"Polygon",
          "coordinates":[[.members[]
            | select(.role=="outer" and .type=="way")
            | .geometry[]
            | [.lon,.lat] ]]}}
       }]}'
> vaucluse.geojson
```

### Create the `.pbf` for Vaucluse

Install `osmium-tool` and run this command:
```bash
osmium extract   --polygon=data/vaucluse.geojson   --output=data/vaucluse-latest.osm.pbf   --overwrite   paca-250917.pbf
```

This command creates a new `.pbf` file containing only the **Vaucluse** data.

This will create a **`vaucluse.geojson`** file containing the administrative boundary of the department.

