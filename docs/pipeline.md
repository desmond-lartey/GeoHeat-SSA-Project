# Pipeline

The analysis runs as four independent Google Earth Engine (GEE) scripts.
They share no state at the script level — each is self-contained and can be
run in the GEE Code Editor in any order — but conceptually they follow this
sequence.

## Run order

| Step | Script | Purpose |
|---|---|---|
| 1 | [`01_population_ghs.js`](script-reference.md#01_population_ghsjs) | Establish population context per city, 1975–2030 |
| 2 | [`02_lst_gapfilling_lagos.js`](script-reference.md#02_lst_gapfilling_lagosjs) | Build cloud- and stripe-free annual LST composites (Landsat 7/8, 2000–2024) |
| 3 | [`03_landcover_allcities.js`](script-reference.md#03_landcover_allcitiesjs) | Harmonize and export ESRI land cover (2017–2023) for all four cities |
| 4 | [`04_uhii_comparison.js`](script-reference.md#04_uhii_comparisonjs) | Cross-check the Landsat-derived thermal signal against independent MODIS UHII products |

Steps 2 and 3 produce the raster/vector layers that feed the downstream
grid-based zonal regression and GAM/Spearman correlation analysis described
in [Study Design](study-design.md); step 1 and step 4 provide population
context and an independent validation dataset respectively.

## Step 1 — Population context

`01_population_ghs.js` pulls the **Global Human Settlement Layer population
grid (GHS-POP, R2023A)** for each of the four cities' GAUL Admin-1
boundaries, for every 5-year step from **1975 to 2030**, and sums population
within each boundary via `reduceRegion`. This establishes the demographic
backdrop (population growth, density) against which thermal trends are
interpreted.

## Step 2 — LST composite construction and gap-filling

`02_lst_gapfilling_lagos.js` is the core thermal-retrieval script. For each
year from 2000 to 2024:

1. **Sensor selection** — Landsat 7 (`LE07/C02/T1_L2`) is used through 2012;
   Landsat 8 (`LC08/C02/T1_L2`) from 2013 onward.
2. **Cloud and saturation masking** — the `QA_PIXEL` and `QA_RADSAT` bands
   are used to mask cloud, cloud shadow, and saturated pixels
   (`maskL7sr`/`maskL8sr`).
3. **Scaling** — surface reflectance bands are scaled to physical units
   (`× 0.0000275 − 0.2`); the thermal band (`ST_B6` for Landsat 7,
   `ST_B10` for Landsat 8) is scaled to Kelvin
   (`× 0.00341802 + 149.0`).
4. **Gap-filling (Landsat 7 only)** — SLC-off scan-line gaps are filled
   using a cascade of increasing-radius focal-mean smoothing (radii 1–3
   pixels) followed by Gaussian-kernel smoothing, applied via
   `image.unmask()` fallbacks (`fillGaps`).
5. **Compositing** — for years ≤ 2012, a **±3-year window** around the
   target year is pooled and reduced with `.median()` to compensate for
   Landsat 7's degraded coverage; for years ≥ 2013, a single calendar-year
   Landsat 8 composite is used directly (median of images with
   `CLOUD_COVER < 10`).
6. **Export** — each year's composite is clipped to the city AOI and
   exported to Drive as a multi-band GeoTIFF (30 m, `fileDimensions: 7680`).

!!! note "Per-city runs"
    The script is parameterized around a single Area of Interest (AOI),
    shown here for Lagos. The same logic is re-run per city by swapping the
    GAUL filter (`ADM1_NAME`/`ADM0_NAME`).

## Step 3 — Land cover harmonization

`03_landcover_allcities.js` filters the **ESRI Global Land Cover** image
collection (10 m, Sentinel-2 derived) by calendar year (2017–2023) for each
of the four cities, mosaics same-year tiles, clips to the GAUL Admin-1
boundary, and remaps to a **6-class subset**:

- `1` — Water
- `2`, `4`, `9` — Vegetation (Trees, Flooded Vegetation, Rangeland)
- `5` — Built Area
- `6` — Bare Ground

Pixels outside this subset are masked out (`selfMask()`), and each
city/year combination is exported to Drive as a GeoTIFF at 30 m. A
categorical legend widget is built for interactive inspection in the GEE
Code Editor.

## Step 4 — Independent UHI validation

`04_uhii_comparison.js` pulls eight pre-computed **Urban Heat Island
Intensity (UHII)** datasets (day/night, Aqua/Terra, multiple algorithm
variants, from `projects/sat-io/open-datasets/UHII`), clips each to the
four city boundaries, computes summary statistics (mean, min, max, std
dev), and exports both a statistics CSV and per-city/per-dataset GeoTIFFs.
This provides an independent, MODIS-derived benchmark against which the
Landsat-derived LST/UTFVI results can be sanity-checked.

## Downstream analysis (not GEE)

The exported CSV and GeoTIFF layers are combined outside Earth Engine to:

- Overlay a **1 km² grid** per city and compute zonal built-up %,
  vegetation %, and mean UTFVI per grid cell, per benchmark year.
- Fit **standardized linear regressions** of UTFVI against land-cover
  fractions, per city.
- Fit **GAM** and compute **Spearman's ρ** between LST and NDVI/NDBI/MNDWI,
  per city, for 2000 and 2024.
- Run a **Mann-Kendall trend test** and Sen's slope estimator on each
  city's 2000–2024 mean annual LST series.

See [Data & Outputs](data-outputs.md) for the exact export schema.
