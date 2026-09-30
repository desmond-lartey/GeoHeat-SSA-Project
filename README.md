# Urban Dynamics and GeoAI

**Mapping urban heat dynamics in Sub-Saharan Africa: a multi-city analysis of the drivers of urban heat islands.**

<p align="center">
  <a href="https://github.com/desmond-lartey/GeoHeat-SSA-Project" target="_blank" rel="noopener noreferrer"><img src="https://img.shields.io/badge/status-manuscript-blueviolet" alt="Status"></a>
  <a href="https://github.com/desmond-lartey/GeoHeat-SSA-Project/blob/main/LICENSE" target="_blank" rel="noopener noreferrer"><img src="https://img.shields.io/badge/license-MIT-green" alt="License"></a>
  <img src="https://img.shields.io/badge/period-2000–2024-lightgrey" alt="Period">
  <img src="https://img.shields.io/badge/cities-4-orange" alt="Cities">
  <img src="https://img.shields.io/badge/platform-Google%20Earth%20Engine-informational" alt="Platform">
  <img src="https://visitor-badge.laobi.icu/badge?page_id=desmond-lartey.GeoHeat-SSA-Project" alt="Visitors">
</p>

Urban heat island (UHI) effects are intensifying across Sub-Saharan Africa
(SSA), raising climate vulnerability in rapidly growing cities with limited
adaptive capacity, yet comparative evidence on what actually drives urban
heat across the region remains limited. This project maps the spatiotemporal
dynamics of urban heat and the influence of land-use and land-cover (LULC)
change on thermal stress in **Johannesburg, Nairobi, Lagos, and Kinshasa**
between **2000 and 2024**, using Landsat-derived land surface temperature
(LST), open-access LULC datasets, the **Urban Thermal Field Variance Index
(UTFVI)**, and grid-based spatial-statistical analysis to relate urban heat
to built-up area, vegetation, and water cover.

This repository holds the companion Google Earth Engine (GEE) scripts for
the manuscript:

> Mapping urban heat dynamics in Sub-Saharan Africa: a multi-city analysis of
> drivers of urban heat island across African cities. *Manuscript in
> preparation, Desmond Lartey et al.*

## What this study does

- **Retrieves Landsat-derived land surface temperature (LST)** from Landsat
  7 (2000–2012) and Landsat 8 (2013–2024), converting thermal bands to
  brightness temperature via sensor-specific calibration constants (K1, K2)
  and then to LST (°C) through the standard blackbody equation.
- **Gap-fills Landsat 7 SLC-off imagery** with multi-scale focal-mean and
  Gaussian-smoothed composites, blended with multi-year (±3 year) image
  windows to produce cloud- and stripe-free annual composites.
- **Harmonizes ESRI 2017–2023 Global Land Cover (10 m, Sentinel-2)** into a
  5-class scheme (Water, Vegetation, Built Area, Bare Ground) per city, per
  year, clipped to GAUL Admin-1 boundaries.
- **Computes the Urban Thermal Field Variance Index (UTFVI)** — LST
  normalized by each city's own mean and standard deviation — as a
  climate-independent, cross-city comparable proxy for urban thermal stress.
- **Derives NDVI, NDBI, and MNDWI spectral indices** from Landsat surface
  reflectance to characterize vegetation, built-up, and water signal at
  30 m resolution.
- **Runs a 1 km² grid-based zonal regression** linking percentage built-up
  area, percentage vegetation, and mean UTFVI change between benchmark
  years (2000, 2010, 2020, 2024).
- **Fits GAM and Spearman's rank correlation models** between LST and
  spectral indices (NDVI, NDBI, MNDWI) for each city, and applies a
  Mann-Kendall trend test to the 2000–2024 LST time series.
- **Extracts population context** from the Global Human Settlement Layer
  (GHS-POP, 1975–2030) for each city's administrative boundary.
- **Compares external Urban Heat Island Intensity (UHII) datasets** (MODIS
  Aqua/Terra day/night composites) as an independent cross-check on the
  Landsat-derived thermal signal.

## Main findings

- **Kinshasa recorded the largest thermal increase** — mean LST rose from
  28.7 °C in 2000 to 33.9 °C in 2024 — while **Johannesburg's mean LST fell**
  from 32.8 °C to 27.4 °C over the same period.
- **Temperature trends are rising and statistically significant in Nairobi,
  Lagos, and Kinshasa**, and significantly declining in Johannesburg; only
  Nairobi's trend is not statistically significant.
- **Built-up expansion is the strongest, most consistent driver of thermal
  stress across all four cities**, with the effect strongest in
  Johannesburg (r = +0.56) and present but weaker in Nairobi (r = +0.39),
  Lagos, and Kinshasa.
- **Vegetation cover cools consistently across cities** — the strongest
  effect is in Nairobi (r = −0.36) — while water bodies provide smaller and
  more spatially variable thermal relief, especially in Lagos and Kinshasa.
- **Higher NDBI tracks higher LST and higher NDVI/MNDWI track lower LST**
  in nearly every city-year combination, corroborating the UTFVI-based
  regression results with an independent spectral-index analysis.
- **Vegetation-to-built-up land transitions produce the sharpest UTFVI
  increases**, reinforcing green and blue infrastructure as priority
  interventions for thermal resilience in rapidly urbanizing SSA cities.
