<div align="center">
  <img src="assets/smra-header.jpg" alt="South Mountain Radio Amateurs — N3TWT — Carlisle, PA" width="620">
</div>

<h1 align="center">EmComm Mapping Demo</h1>

<p align="center">
  <strong>QGIS for Emergency Communications — a starter kit for SMRA operators</strong><br>
  Who can talk to whom · Where stations and assets are · What happened after the net
</p>

<p align="center">
  <a href="https://www.qgis.org"><img src="https://img.shields.io/badge/QGIS-3.44%20Solothurn-62a05f?logo=qgis&logoColor=white" alt="QGIS 3.44"></a>
  <a href="https://n3twt.org"><img src="https://img.shields.io/badge/Club-N3TWT.org-1f6feb" alt="N3TWT"></a>
  <a href="https://github.com/KC3DRU"><img src="https://img.shields.io/badge/Maintainer-KC3DRU-333333" alt="Maintainer KC3DRU"></a>
  <img src="https://img.shields.io/badge/Data-CSV%20%C2%B7%20WKT%20%C2%B7%20GeoJSON%20%C2%B7%20Shapefile-6f42c1" alt="Data formats">
  <img src="https://img.shields.io/badge/Cost-100%25%20free%20%2F%20open%20source-2ea44f" alt="Free and open source">
</p>

<p align="center">
  Companion files for the club presentation <em>“Intro to QGIS for Emergency Communications Mapping”</em>.<br>
  Clone this repo, open <code>EmComm_Planning_Map.qgz</code>, and you have a working map in about five minutes.
</p>

---

## Contents

- [What this is](#what-this-is)
- [Quick start](#quick-start)
- [What's in the repo](#whats-in-the-repo)
- [The sample project](#the-sample-project)
- [Make your own net map](#make-your-own-net-map)
- [Styling cheat sheet](#styling-cheat-sheet)
- [QGIS → CalTopo handoff](#qgis--caltopo-handoff)
- [The presentation](#the-presentation)
- [Known issues & housekeeping](#known-issues--housekeeping)
- [Data sources worth knowing](#data-sources-worth-knowing)
- [Field tips](#field-tips)
- [Contributing & contact](#contributing--contact)

---

## What this is

A **real, working QGIS project** built from an actual SMRA exercise, packaged so any operator in the club can open it, follow along with the slides, and then point it at their own data.

During the **July 2026 simplex test** handled out of the Cumberland County EOC, the question was simple: *who could we actually hear?* A spreadsheet of callsigns and coordinates can answer that — a map answers it instantly.

<div align="center">
  <img src="assets/net-connectivity-july-2026.png" alt="QGIS map of stations heard during the July 2026 SMRA simplex test, drawn as lines radiating from the EOC" width="560">
  <br><sub>Stations heard on the July 2026 net — one line per contact, radiating from the EOC.</sub>
</div>

**What QGIS is good for in EmComm:**

| Use it for | Reach for something else when |
| :-- | :-- |
| Custom situational maps — any layers you want | You need **live** APRS positions → YAAC, Xastir, APRSISCE |
| After-action maps for net and exercise reports | You need shared, mobile, real-time maps → CalTopo |
| Combining radio data + county GIS + OpenStreetMap | You need coverage prediction / radio-specific tooling |
| Rich symbology, labeling, print layouts (PDF/11×17) | — |

> QGIS **complements** live tracking tools; it does not replace them. Do the careful layer work in QGIS, then push the result to CalTopo for field sharing.

---

## Quick start

1. **Install QGIS** (free) from [qgis.org](https://www.qgis.org) — Windows, macOS, Linux, even Raspberry Pi. This project was saved with **QGIS 3.44 Solothurn**; any recent 3.x will open it.
2. **Clone or download** this repository:
   ```bash
   git clone https://github.com/KC3DRU/EmComm-Mapping-Demo.git
   cd EmComm-Mapping-Demo
   ```
   > Keep the folder structure intact — the project references data with **relative paths** (`./Layers/...`).
3. **Double-click** `EmComm_Planning_Map.qgz` (or QGIS → *Project → Open*).
4. **Toggle layers** in the Layers panel. Turn basemaps on/off to reduce visual noise — OSM, USGS Topo, and ESRI World Hillshade are all included.
5. **Frame the data**: right-click a layer → *Zoom to Layer*, or use the **Zoom to Full Extent** toolbar button to see everything at once.

> **Internet required** for the three basemaps (they are online XYZ tile services). Your radio layers work offline regardless — see [Field tips](#field-tips) for caching tiles ahead of a deployment.

---

## What's in the repo

```text
EmComm-Mapping-Demo/
├── EmComm_Planning_Map.qgz              # The QGIS project (layers, styles, CRS)
├── QGIS_EmComm_Mapping_Intro_v2.odp     # Club presentation (LibreOffice Impress)
├── QGIS_EmComm_Mapping_Intro_v2.pptx    # Same deck for PowerPoint
├── Layers/                              # Working data used by the project
│   ├── 20260716_EmComm_Net_EOC_South_Mountain.csv   # Net contacts as WKT linestrings
│   ├── OPERATORS.WKT                    # Station points (callsign + WKT POINT)
│   ├── EC_DRILL.WKT                     # Drill contacts (callsign + WKT + SIGNAL)
│   ├── Merged_Drill_Layers.geojson      # Merged input for network analysis
│   ├── ShortestPath.geojson             # Output of the Shortest Path tool
│   ├── N3TWT_Repeaters.*                # Club repeater points (ESRI Shapefile set)
│   └── TM Support Sites.sqlite          # County support-site points (SpatiaLite)
├── Exports/                             # Sample exports for sharing / CalTopo
│   ├── N3TWT_Repeaters.geojson
│   └── N3TWT_Repeaters.qmd              # QGIS layer definition metadata
└── assets/                              # Images used by this README
```

**Project CRS:** `EPSG:4326` (WGS 84) — the same lat/lon datum your GPS and APRS use.

---

## The sample project

| Layer | Geometry | Source file | What it shows |
| :-- | :-- | :-- | :-- |
| `20260716_EmComm_Net_EOC_South_Mountain` | Lines | `Layers/…csv` | Every station worked on the July 2026 net, drawn from the EOC (21 features) |
| `OPERATORS` | Points | `Layers/OPERATORS.WKT` | Station locations labelled with callsigns |
| `EC_DRILL-KC3DRU Reports` | Lines | `Layers/EC_DRILL.WKT` | One operator's point-to-point drill results, with `SIGNAL` |
| `Merged Drill Layers` | Lines | `Layers/Merged_Drill_Layers.geojson` | All contact layers merged, ready for analysis |
| `Shortest path` | Line | `Layers/ShortestPath.geojson` | Best multi-hop route between two stations (relay planning) |
| `N3TWT_Repeaters` | Points | `Layers/N3TWT_Repeaters.shp` | Four club repeaters: `146.460`, `145.430`, `443.000`, `146.685 (DMR)` |
| `TMI Support Sites` | Points | `Layers/TM Support Sites.sqlite` | EOC, Shipp Univ. Recovery Center, Lower Allen Twp. Office, New Cumberland Bur. Off. |
| `OpenStreetMap` · `USGS topo` · `ESRI World Hillshade` | Raster tiles | Online XYZ | Interchangeable basemaps |

<div align="center">
  <img src="assets/shortest-path-analysis.png" alt="Shortest path analysis in QGIS showing the best relay route between two stations" width="600">
  <br><sub>Merging contact layers and running <em>Shortest Path</em> answers: “what is the best simplex route to reach this station?”</sub>
</div>

---

## Make your own net map

The whole demo runs on **one CSV file**. No database, no plugins, no cost.

### 1. Log the net in a spreadsheet

Columns: `CALLSIGN`, `WKT`, `SIGNAL`. Each row is a contact drawn as a `LINESTRING` from *your* location (or the net control station) to the station you worked:

```csv
CALLSIGN,WKT,SIGNAL
EOC_NCO,"LINESTRING(-77.16001461 40.21293984 , -77.16001461 40.21293984)",GOOD
WB3FNJ,"LINESTRING(-77.16001461 40.21293984 , -77.166066   40.265184  )",GOOD
KC3ZPB,"LINESTRING(-77.16001461 40.21293984 , -77.131887   40.258217 )",POOR
```

Notes that save grief later:

- Longitude first, latitude second, space between them — that is WKT.
- Keep the coordinate **precision you actually have**; four decimals is ~11 m, plenty for a net map.
- Quote the WKT field (it contains a comma). Leading spaces before the quote are tolerated, but avoid them.
- For a plain station list instead of contacts, use `POINT(lon lat)` — see `Layers/OPERATORS.WKT`.

### 2. Add it to QGIS

`Layer → Add Layer → Add Delimited Text Layer` → pick the file, set **Geometry definition = Well-known text (WKT)**, geometry field = `WKT`, CRS = `EPSG:4326` → *Add*.

### 3. Style by signal, label with callsigns

- **Symbology → Categorized** → classify on `SIGNAL` → assign a color per class.
- **Labels → Single Labels** → value = `CALLSIGN` → place them at the line endpoints so each end reads clearly.
- One clear question per map. If you can't read it at arm's length on a printed page, it's too many layers.

---

## Styling cheat sheet

The convention used in the deck (Layer Properties → Symbology → *Categorized* or *Rule-based*):

| Signal | Color | Meaning |
| :-- | :-- | :-- |
| Strong / `GOOD` | 🟢 Solid green | Clear copy, reliable |
| Fair | 🟠 Amber | Usable with effort / QSB |
| Weak / `POOR` | 🔴 Red | Marginal — expect to need a relay |

Keep the palette to three classes and put the legend on the layout. Under stress, in bad light, on a printed page: subtle palettes fail.

---

## QGIS → CalTopo handoff

Do the careful work in QGIS, then share live with the field team:

**1. Export from QGIS**

- Reproject the layer to **WGS 84 (`EPSG:4326`)**.
- Make sure a `Name` field (KML) or `title` field (GeoJSON) holds your labels — `Exports/N3TWT_Repeaters.geojson` shows the pattern.
- Right-click layer → *Export → Save Features As…* → **GPX** (routes/tracks/waypoints), **KML** (all-round, keeps attributes) or **GeoJSON** (preserves the most structure).

**2. Import into CalTopo**

- Open or create your map at [caltopo.com](https://caltopo.com) → **Map Objects → Import** → choose the file → tick the features → *Import*.
- Share the map by link or QR code so field teams see updates live.

> Tip: export windshield-survey routes as **GPX tracks** and resource locations as **GPX waypoints** or KML points for the cleanest results.

---

## The presentation

`QGIS_EmComm_Mapping_Intro_v2.odp` (LibreOffice Impress) and the `.pptx` copy — 27 slides, delivered to the club October 2026 by Rick Stoner, KC3DRU.

1. **Why maps matter in EmComm** — situational awareness, after-action, planning
2. **Why QGIS (and when not)** — open-source power vs. specialized radio tools
3. **Core GIS concepts in 5 minutes** — layers, vectors, CRS, attributes, symbology, labels
4. **Hands-on workflow** — points, lines, labels, signal-strength styling, shortest path
5. **Data, offline use & next steps** — sources, print layouts, CalTopo, resources

Slides 10–13 also show how much a basemap changes the picture: aeronautical, nautical, geological, and an [azimuthal projection](https://ns6t.net/azimuth/) centered on your QTH.

---

## Known issues & housekeeping

- **`Merged Drill Layers` may load as invalid.** The project stores it as `./Merged_Drill_Layers.geojson` while the file lives in `Layers/`. Fix in QGIS by right-clicking the layer → *Fix Layer* (or remove it and re-add `Layers/Merged_Drill_Layers.geojson`); the merge is reproducible with *Processing → Merge vector layers*.
- **Basemaps need internet.** OSM, USGS Topo and ESRI World Hillshade are online tile services — plan for this before an activation.
- **Sample data contains real callsigns tied to locations.** It is published here as a learning artifact; treat operator positions with the same care you would treat any net log, and strip or generalize them before republishing anywhere public.
- **Leftovers in the tree:** `Layers/oldCrap/` holds an empty scratch database and can be deleted; `Exports/` is a demo of export options, not required for the project to run.
- **No license file yet.** Until one is added, treat the deck and data as club-internal material. Ask KC3DRU before reposting outside SMRA.

---

## Data sources worth knowing

| Source | What you get |
| :-- | :-- |
| [OpenStreetMap](https://www.openstreetmap.org) | Free basemap data & tiles; cache them for offline use |
| [PASDA](https://www.pasda.psu.edu) | Free Pennsylvania GIS — counties, EOCs, flood, roads |
| [Cumberland County GIS](https://www.cumberlandcountypa.gov/3517/Map-Services) | Parcels, roads, buildings, emergency facilities |
| [York County GIS](https://york-county-pa-gis-portal-yorkcountypa.hub.arcgis.com/) | Same, one county over |
| [QuickMapServices](https://plugins.qgis.org/plugins/QuickMapServices/) | QGIS plugin — one click to dozens of basemaps |
| GPX from handhelds / APRS exports | Track logs and waypoints straight into QGIS |
| Repeater directories / FCC data | Public databases → convert to a point layer |
| Your own digitizing | Click to add stations, draw contact lines |

---

## Field tips

- **Offline first.** Pre-download basemap tiles, or use a local GeoPackage / MBTiles, so the map works with no internet.
- **Print layouts.** Layout Manager → clean 8.5×11 or 11×17 with legend, scale bar and north arrow for the field kit.
- **Date every map.** Put "as of" date/time on the layout — situations change fast and an undated map becomes a liability.
- **Simple beats complete.** One question per map: who could we hear, where are the shelters, which routes are open.
- **Share the project, not a screenshot.** A `.qgz` plus the `Layers/` folder hands the whole capability to the next operator.

---

## Contributing & contact

If you built a map for a net, exercise, or Field Day — put it in a branch and open a pull request. Especially welcome:

- More net/exercise data (anonymized or generalized as the operator prefers)
- A ready-to-print 11×17 layout template for Cumberland County
- A repeater coverage layer, with tones and coverage caveats documented
- Offline tile-packaging notes for the county

**South Mountain Radio Amateurs (N3TWT)** — ARRL Special Service Club serving Carlisle, Camp Hill and Greater Cumberland County, PA.

| | |
| :-- | :-- |
| 🌐 Club site | [n3twt.org](https://n3twt.org) |
| 📻 Nets & repeater tones | [n3twt.org/nets](https://n3twt.org/nets) |
| 🛡️ ARES / EmComm | [n3twt.org → ARES](https://n3twt.org) |
| 📍 Meetings | Cumberland County EOC, 1 Public Safety Drive, Carlisle, PA |
| ✉️ Project contact | Rick Stoner, KC3DRU — [github.com/KC3DRU](https://github.com/KC3DRU) |

*Happy hunting — and may your next net map be a full one.* **73, SMRA**
