# vienna-trails-geodatabase

> ArcGIS geodatabase template for hiking trail datasets — with subtypes, coded value domains, and range domains pre-configured for attribute validation and web publishing.

[![ArcGIS Pro](https://img.shields.io/badge/ArcGIS%20Pro-3.1%2B-0079C1?style=flat-square)](https://www.esri.com/arcgis/products/arcgis-pro)
[![License: CC BY-NC-SA](https://img.shields.io/badge/License-CC%20BY--NC--SA-lightgrey?style=flat-square)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
[![Data: Open Gov Vienna](https://img.shields.io/badge/Data-Open%20Gov%20Vienna-green?style=flat-square)](https://data.wien.gv.at)

---

## Overview

This repository contains a file geodatabase (`vienna.gdb`) and an ArcGIS Pro project package (`Vienna.ppkx`) built around the City of Vienna's publicly available hiking trail data. The schema demonstrates production-ready geodatabase design patterns:

- **Subtypes** on the `TrailRating` field to categorize trails as Easy, Moderate, Strenuous, or Unrated
- **Coded value domains** for accessibility type, scenery, trail type, elevation change, and duration
- **Range domains** for trail length (scoped per subtype) and walk time
- **Feature templates** pre-configured for all four subtypes, ready for web publishing

The finished layer is designed to be published as a hosted feature service on ArcGIS Online, with full domain and subtype support carried through to Map Viewer editing.

---

## Repository Structure

```
vienna-trails-geodatabase/
├── Vienna.ppkx                     # ArcGIS Pro project package (open directly)
├── vienna.gdb/                     # File geodatabase
│   ├── ViennaCityTrails_Raw        # Source feature class (line)
│   ├── RundumadumTrail_Subtypes    # Rundumadum trail stages (subtype example)
│   ├── Duration                    # Reference table → TrailDuration domain
│   ├── ElevationChange             # Reference table → ElevationChange domain
│   ├── Scenery                     # Reference table → Scenery domain
│   ├── TrailLength                 # Reference table → TrailLength domain
│   └── TrailType                   # Reference table → TrailType domain
├── docs/
│   ├── schema.md                   # Full field and domain reference
│   └── publish-guide.md            # Step-by-step ArcGIS Online publish instructions
└── README.md
```

---

## Geodatabase Schema

### Feature Class: `ViennaCityTrails_Raw`

| Field Name       | Alias                   | Type   | Domain / Subtype       |
|------------------|-------------------------|--------|------------------------|
| `BEZ_TEXT`       | Original Name           | Text   | —                      |
| `URL_INFO`       | Trail Website           | Text   | —                      |
| `TrailName`      | Trail Name              | Text   | —                      |
| `TrailStart`     | Trail Start             | Text   | —                      |
| `TrailEnd`       | Trail End               | Text   | —                      |
| `Duration`       | Estimated Time          | Text   | —                      |
| `Facilities`     | Facilities              | Text   | —                      |
| `RouteDescription` | Route Description     | Text   | —                      |
| `MinDuration`    | Minimum Walk Time       | Short  | `MinTrailDuration`     |
| `MaxDuration`    | Maximum Walk Time       | Short  | `MaxTrailDuration`     |
| `Length`         | Trail Length            | Short  | Per-subtype (see below)|
| `Accessibility`  | Trail Accessibility     | Short  | `Accessibility`        |
| `Scenery`        | Highlighted Scenery     | Text   | `Scenery`              |
| `Elevation`      | Average Trail Elevation | Short  | `ElevationChange`      |
| `TrailType`      | Type of trail           | Text   | `TrailType`            |
| `TrailCode`      | Trail Code              | Short  | —                      |
| `**TrailRating**`| **Trail Rating**        | Short  | **SUBTYPE FIELD**      |

> `**TrailRating**` is the subtype field. Its value drives which domains apply to `Length`, `MinDuration`, and `MaxDuration`.

---

## Subtypes

Subtypes are applied to the `TrailRating` (Short) field. The default subtype for new features is **Unrated**.

| Code | Description | Length Domain       | Min Duration | Max Duration |
|------|-------------|---------------------|--------------|--------------|
| 1    | Easy        | `ShortTrailLength`  | 1–2 hrs      | 3–7 hrs      |
| 2    | Moderate    | `MediumTrailLength` | 1–2 hrs      | 3–7 hrs      |
| 3    | Strenuous   | `LongTrailLength`   | 1–2 hrs      | 3–7 hrs      |
| 4    | Unrated     | `ShortTrailLength`  | 1–2 hrs      | 3–7 hrs      |

---

## Domains

### Coded Value Domains

#### `Accessibility`
| Code | Description        |
|------|--------------------|
| 1    | Unpaved            |
| 2    | Paved              |
| 3    | Partially Paved    |
| 4    | Stroller Friendly  |
| 5    | Wheelchair Friendly|

#### `TrailDuration`
| Code | Description  |
|------|--------------|
| 1    | < 20 min     |
| 2    | < 1 hour     |
| 3    | 1–2 hours    |
| 4    | 3–4 hours    |
| 5    | > 5 hours    |

> `Scenery`, `TrailLength`, `ElevationChange`, and `TrailType` coded value domains are stored in their respective reference tables and loaded via the **Table To Domain** geoprocessing tool. See `docs/schema.md` for full code listings.

### Range Domains

| Domain Name        | Description                          | Field Type | Min | Max |
|--------------------|--------------------------------------|------------|-----|-----|
| `ShortTrailLength` | Avg. km length of a short trail      | Short      | 2   | 9   |
| `MediumTrailLength`| Avg. km length of a medium trail     | Short      | 10  | 13  |
| `LongTrailLength`  | Avg. km length of a long trail       | Short      | 14  | 15  |
| `MinTrailDuration` | Minimum trail duration (hours)       | Short      | 1   | 2   |
| `MaxTrailDuration` | Maximum trail duration (hours)       | Short      | 3   | 7   |

---

## Getting Started

### Requirements

- ArcGIS Pro 3.1 or later
- ArcGIS organizational account (Publisher or Administrator role) for web publishing

### Open the project

1. Clone or download this repository.
2. Double-click `Vienna.ppkx` — ArcGIS Pro will unpack and open the project automatically.
3. Sign in with your ArcGIS account if prompted.

### Explore the schema

In the **Catalog** pane, expand **Databases → vienna.gdb**. Right-click any feature class and choose **Data Design → Subtypes** or **Data Design → Domains** to inspect the configured validation rules.

### Publish to ArcGIS Online

See [`docs/publish-guide.md`](docs/publish-guide.md) for a full walkthrough. The short version:

1. In the **Contents** pane, keep only the `ViennaCityTrails` layer visible.
2. Go to **Edit → Manage Templates → Create all templates** on the `ViennaCityTrails` layer.
3. Go to **Share → Web Layer → Publish Web Layer**.
4. Set Layer Type to **Feature**, choose a destination folder, and share with your organization or public.
5. After publishing, enable editing on the item page under **Settings → Feature Layer (hosted) → Enable editing**.

---

## Data Sources

- Trail geometry and base attributes: [City of Vienna Open Government Data](https://data.wien.gv.at) — *Wanderwege* shapefile, licensed under OGD Austria.
- `Duration`, `ElevationChange`, `Scenery`, `TrailLength`, and `TrailType` reference tables: derived from City of Vienna hiking trail descriptions.
- Basemap: [basemap.at](https://basemap.at) Austrian OGD basemap.

---

## License

Trail data is derived from City of Vienna OGD — see [data.wien.gv.at](https://data.wien.gv.at) for terms.  
Schema design, reference tables, and documentation in this repository are licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).

---

## Contributing

Pull requests are welcome for schema improvements, additional reference tables, or documentation fixes. Please open an issue first for significant schema changes so the impact on existing published services can be discussed.
