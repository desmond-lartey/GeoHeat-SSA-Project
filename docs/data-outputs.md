# Data & Outputs

## Export destinations

All Earth Engine exports in this project target a Google Drive folder
named **`modelling`**. `01_population_ghs.js` prints results to the Code
Editor console rather than exporting; add an `Export.table.toDrive` call if
a persistent CSV of population totals is needed.

## Output inventory

| Script | Output | Format | Count | Resolution |
|---|---|---|---|---|
| `02_lst_gapfilling_lagos.js` | `{City}_GapFilled_{year}.tif` | GeoTIFF, multi-band (SR + thermal) | 1 city × 25 years/run | 30 m |
| `03_landcover_allcities.js` | `{City}_{Year}_LULC_10m.tif` | GeoTIFF, single-band, 6-class | 4 cities × 7 years = 28 | 30 m |
| `04_uhii_comparison.js` | `UHII_Statistics_Cities.csv` | CSV | 1 file, 32 rows (8 datasets × 4 cities) | — |
| `04_uhii_comparison.js` | `UHII_{dataset}_{city}.tif` | GeoTIFF | 8 datasets × 4 cities = 32 | 30 m |

## LULC class schema

| Code | Class | Notes |
|---|---|---|
| 1 | Water | |
| 2, 4, 9 | Vegetation | Trees, Flooded Vegetation, Rangeland aggregated |
| 5 | Built Area | Primary predictor for thermal stress |
| 6 | Bare Ground | |

All other ESRI class codes (Snow/Ice, Clouds) are masked out prior to
export.

## `UHII_Statistics_Cities.csv` schema

| Column | Description |
|---|---|
| `City` | One of Lagos, Nairobi, Johannesburg, Kinshasa |
| `Dataset` | One of `UHII_AMOD2`, `UHII_MOD1`, `UHII_MOD2`, `UHII_MYD1`, `UHII_MYD2`, `UHII_SAT`, `UHII_SMOD2`, `UHII_SMYD1` |
| `mean` | Mean UHII value over the city boundary |
| `min` | Minimum UHII value |
| `max` | Maximum UHII value |
| `stdDev` | Standard deviation of UHII values |

## Downstream analysis outputs (not exported by GEE)

The 1 km² grid-based zonal regression, GAM/Spearman correlation tables, and
Mann-Kendall trend results reported in [Findings](findings.md) are produced
from the GeoTIFF/CSV exports above using standard Python/R geospatial and
statistical tooling (grid overlay, zonal statistics, `mgcv`/`pyGAM`,
`scipy.stats.spearmanr`, `pymannkendall`). Reproducing the manuscript's
Table 3 (GAM/Spearman results, 4 cities × 3 indices × 2 years) requires
joining the per-city LST composites from `02_lst_gapfilling_lagos.js` with
NDVI/NDBI/MNDWI computed from the same Landsat surface reflectance bands.

## Legend reference

An extended, general-purpose land-cover legend (`legend.xlsx`, Dynamic
World / ESRI style class-and-color reference used during exploratory
mapping) is included in the repository for reference; the analysis itself
uses only the 6-class subset above.
