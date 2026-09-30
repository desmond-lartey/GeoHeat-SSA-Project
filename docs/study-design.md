# Study Design

## Objective

To characterize the spatiotemporal dynamics of urban heat across four Sub-
Saharan African (SSA) cities between 2000 and 2024, and to quantify how
land-use and land-cover (LULC) change — particularly built-up expansion,
vegetation loss, and water cover — drives thermal stress, using a
climate-normalized thermal index (UTFVI) that supports valid cross-city
comparison.

## Study cities

Four cities were selected to represent SSA's major climatic zones and
urbanization trajectories:

| City | Region | Climate zone | Population (2024 est.) | Primary heat drivers | UHI risk |
|---|---|---|---|---|---|
| Johannesburg | Southern Africa | Subtropical highland | ~6 million | Surface sealing, tree loss, vehicular emissions | Moderate |
| Nairobi | East Africa | Tropical savanna | ~5 million | Vegetation loss, informal sprawl | High |
| Lagos | West Africa | Humid subtropical | ~22 million | Impervious surfaces, dense settlements | Very High |
| Kinshasa | Central Africa | Tropical rainforest | ~16 million | Deforestation, ecological fragmentation | High |

City boundaries are the FAO GAUL 2015 Admin Level-1 regions matching each
city (`Lagos`/Nigeria, `Nairobi`/Kenya, `Gauteng`/South Africa, and
`Kinshasa`/DR Congo).

## Study period

**2000–2024**, spanning the Landsat 7 → Landsat 8 sensor transition
(Landsat 7 used through 2012, Landsat 8 from 2013 onward), with UTFVI
computed at four benchmark years: **2000, 2010, 2020, and 2024**.

## Datasets

| Data / variable | Source | Resolution | Use |
|---|---|---|---|
| Landsat 7/8 (ETM+/OLI-TIRS), Collection 2 Level-2 | NASA/USGS via Google Earth Engine | 30 m, 16-day revisit (2000–2024) | LST retrieval; NDVI/NDBI/MNDWI computation |
| ESRI 2017–2023 Global Land Cover | Esri Living Atlas (Sentinel-2 derived) | 10 m, annual | LULC classification, built-up/vegetation/water fractions |
| FAO GAUL 2015, Admin Level 1 | FAO | Vector | City administrative boundaries |
| GHS-POP (Global Human Settlement Layer) | JRC / European Commission | 100 m, 5-year steps (1975–2030) | Population context per city |
| UHII (MODIS Aqua/Terra day/night composites) | `projects/sat-io/open-datasets/UHII` | Variable | Independent cross-check of thermal signal |

## Core indices

- **Land Surface Temperature (LST)** — Landsat thermal band converted to
  at-sensor radiance, then brightness temperature (K) via sensor-specific
  calibration constants (K1, K2), then to °C via the standard blackbody
  equation.
- **Urban Thermal Field Variance Index (UTFVI)** — LST standardized by
  each city's own mean and standard deviation
  (`UTFVI = (LST − mean_LST) / std_LST`). Because raw LST is confounded by
  each city's background climate (Johannesburg's subtropical highland
  climate runs systematically cooler than Lagos's humid subtropical
  climate regardless of urban heat intensity), UTFVI isolates the *urban*
  heat signal from *regional* climate and is used as the primary
  comparative metric across cities.
- **NDVI, NDBI, MNDWI** — standard Landsat spectral indices for
  vegetation, built-up, and water/moisture, used to corroborate the
  UTFVI-based LULC relationships with an independent spectral analysis.

## Analytical design

1. Annual/benchmark-year LST and UTFVI composites per city.
2. LULC harmonization and classification (5-class ESRI subset) per city,
   per year.
3. A 1 km² grid overlaid on each city; within each cell, zonal percentage
   built-up, percentage vegetation, and mean UTFVI are computed for each
   benchmark year, and change metrics (percentage-point difference for
   land cover, absolute difference for UTFVI) are derived between years.
4. Standardized linear regression of UTFVI against built-up %, vegetation
   %, and water % per city, reporting correlation coefficients (r) and
   significance (p).
5. Generalized additive models (GAM) and Spearman's rank correlation
   between LST and each spectral index (NDVI, NDBI, MNDWI), fitted
   separately for 2000 and 2024.
6. Mann-Kendall trend test and Sen's slope on the 2000–2024 mean annual
   LST time series per city.

!!! note "Scope"
    This site documents the Google Earth Engine (GEE) scripts used for
    data acquisition, preprocessing, and export. Statistical modeling
    (GAM, Spearman, Mann-Kendall) and the final regression figures were
    produced downstream from the exported CSV/GeoTIFF outputs — see
    [Data & Outputs](data-outputs.md).
