# Script Reference

All scripts are Google Earth Engine (GEE) JavaScript, run in the
[GEE Code Editor](https://code.earthengine.google.com/). Source files live
in [`gee-scripts/`](https://github.com/desmond-lartey/GeoHeat-SSA-Project/tree/Fires/gee-scripts).

## `01_population_ghs.js`

Computes population totals per city from the Global Human Settlement Layer.

**Inputs**

- `FAO/GAUL/2015/level1` - administrative boundaries
- `projects/sat-io/open-datasets/GHS/GHS_POP/GHS_POP_E{year}_GLOBE_R2023A_54009_100_V1_0` - population grid, one image per 5-year step, 1975–2030

**Key variables**

- `years` - `ee.List.sequence(1975, 2030, 5)`
- `cities` - array of `{name, country, adm1_name}` for Lagos, Nairobi, Johannesburg, Kinshasa

**Functions**

| Function | Signature | Behavior |
|---|---|---|
| `calculatePopulation` | `(feature) → ee.Feature` | For a city boundary feature, loops over every year in `years`, sums the GHS-POP grid within the boundary via `reduceRegion({reducer: ee.Reducer.sum(), scale: 100})`, and attaches each year's total as a `pop_{year}` property. |

**Output** - `citiesWithPop`, an `ee.FeatureCollection` with one feature per
city carrying a `pop_{year}` property for every 5-year step, printed to the
Code Editor console (`print`). No `Export` call is included in this script;
add an `Export.table.toDrive` call if a persistent CSV is needed.

---

## `02_lst_gapfilling_lagos.js`

Builds cloud-masked, gap-filled, annual LST-ready composites from Landsat
7/8 surface reflectance and thermal bands.

**Inputs**

- `LANDSAT/LE07/C02/T1_L2` - Landsat 7 Collection 2 Level-2, used for years ≤ 2012
- `LANDSAT/LC08/C02/T1_L2` - Landsat 8 Collection 2 Level-2, used for years ≥ 2013
- `FAO/GAUL/2015/level1`, filtered to `ADM1_NAME = 'Lagos'`, `ADM0_NAME = 'Nigeria'` (swap for other cities)

**Functions**

| Function | Signature | Behavior |
|---|---|---|
| `maskL7sr` / `maskL8sr` | `(image) → ee.Image` | Masks cloud/shadow/cirrus via a bitwise `QA_PIXEL` test (`bitwiseAnd(0b11111).eq(0)`) and masks saturated pixels via `QA_RADSAT.eq(0)`. |
| `scaleImage` | `(image) → ee.Image` | Scales `SR_B*` reflectance bands (`× 0.0000275 − 0.2`); conditionally scales `ST_B6` (Landsat 7) or `ST_B10` (Landsat 8) to Kelvin (`× 0.00341802 + 149.0`) depending on which thermal band is present, using `ee.Algorithms.If`. |
| `fillGaps` | `(image) → ee.Image` | Cascades `focal_mean` at radii 1, 2, 3 pixels (square kernel) and a `Gaussian` convolution (radius 5), each applied as a successive `unmask()` fallback, to fill Landsat 7 SLC-off scan-line gaps. |

**Control flow** - for `year` from 2000 to 2024:

- If `year <= 2012`: pool Landsat 7 images from `year − 3` to `year + 3`,
  filter `CLOUD_COVER < 20`, apply `maskL7sr` → `scaleImage` → `fillGaps`,
  and reduce with `.median()`.
- Else: filter Landsat 8 images for the calendar year, `CLOUD_COVER < 10`,
  apply `maskL8sr` → `scaleImage`, and reduce with `.median()`.

**Output** - per-year `Export.image.toDrive` call, band set
`['SR_B1'..'SR_B5','SR_B7','ST_B6']` for Landsat-7 years or
`['SR_B1'..'SR_B7','ST_B10']` for Landsat-8 years, 30 m, clipped to the
city AOI, `fileDimensions: 7680`, folder `modelling`.

---

## `03_landcover_allcities.js`

Harmonizes ESRI Global Land Cover into a fixed 6-class subset and exports
per city, per year.

**Inputs**

- `esri_lulc10` - ESRI 2017–2023 Global Land Cover image collection (must be
  imported into the script environment; 10 m, Sentinel-2 derived)
- `FAO/GAUL/2015/level1`

**Key variables**

- `classCodes = [1, 2, 4, 5, 6, 9]` - retained ESRI class codes (Water,
  Trees, Flooded Vegetation, Built Area, Bare Ground, Rangeland)
- `dict` - `{names, colors}` lookup used for both the map legend and the
  visualization palette

**Functions**

| Function | Signature | Behavior |
|---|---|---|
| `remapper` | `(image) → ee.Image` | Remaps `classCodes` to themselves (identity remap used purely as a filter), masking every other class code via `.selfMask()`. |
| `addCategoricalLegend` | `(panel, dict, title)` | Builds a `ui.Panel` legend widget in the Code Editor, one colored row per class name. |
| `getLULCLayer` | `(startDate, endDate, year, city) → void` | Filters `esri_lulc10` by date, mosaics same-period tiles, clips to the city's GAUL boundary, applies `remapper`, adds the result as a map layer, and exports it to Drive as `{city}_{year}` (spaces/duplicate underscores normalized). |

**Control flow** - for each of the four cities, `getLULCLayer` is called
once per calendar year from 2017 to 2023 (`'2017-01-01'…'2017-12-31'`, …,
`'2023-01-01'…'2023-12-31'`).

**Output** - 4 cities × 7 years = 28 `Export.image.toDrive` tasks, 30 m,
folder `modelling`, file name `{City}_{Year}_LULC_10m`.

---

## `04_uhii_comparison.js`

Cross-checks the Landsat-derived thermal signal against eight independent
MODIS-derived Urban Heat Island Intensity (UHII) products.

**Inputs**

- `projects/sat-io/open-datasets/UHII/{AMOD2,MOD1,MOD2,MYD1,MYD2,SAT,SMOD2,SMYD1}` - eight UHII image collections (day/night, Terra/Aqua, multiple algorithm variants)
- `FAO/GAUL/2015/level1`

**Functions**

| Function | Signature | Behavior |
|---|---|---|
| `getCityBoundary` | `(city) → ee.Feature` | Filters GAUL to the city's `ADM0_NAME`/`ADM1_NAME` and wraps the geometry as a feature with `city`/`country`/`adm1` properties. |
| `visualizeUHII` | `(dataset, city) → void` | Computes the dataset's median composite, clips to the city bounds, masks non-positive values, and adds it as a map layer. |
| `computeStatistics` | `(dataset, city) → ee.Feature` | `reduceRegion` with a combined `mean` + `min` + `max` + `stdDev` reducer over the city geometry at 30 m; returns a feature tagged with `Dataset` and `City`. |
| `formatName` | `(name) → string` | Lower-cases and underscores a string for use in export file names. |

**Output**

- One `Export.table.toDrive` call - `UHII_Statistics_Cities.csv` - with
  mean/min/max/stdDev for every dataset × city combination (32 rows).
- 8 datasets × 4 cities = 32 `Export.image.toDrive` GeoTIFF tasks, 30 m,
  folder `modelling`, named `UHII_{dataset}_{city}`.
