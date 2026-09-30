# Findings

## Spatiotemporal thermal dynamics, 2000–2024

| City | Mean LST 2000 | Mean LST 2024 | Trend direction | Statistically significant? |
|---|---|---|---|---|
| Kinshasa | 28.7 °C | 33.9 °C | Rising (largest increase) | Yes |
| Lagos | — | — | Rising | Yes |
| Nairobi | — | — | Rising | No |
| Johannesburg | 32.8 °C | 27.4 °C | Falling | Yes |

Mean temperatures were consistently higher in Nairobi and lowest in
Johannesburg from 2007 onward. Mann-Kendall trend and Sen's slope analyses
show a positive (warming) slope in all cities except Johannesburg, where
the slope is negative. These trends are statistically significant in every
city except Nairobi.

## UTFVI (urban thermal field variance) evolution

- From 2000→2010→2020, the extent of the *most* thermally stressed
  locations generally **decreased** across cities — except in **Lagos**,
  where it did not.
- From 2020→2024, the extent of the most stressed locations **increased
  in all four cities**.
- Over the same 2020→2024 window, moderately stressed locations
  **decreased** in Johannesburg and Lagos, stayed **stable** in Nairobi,
  and **increased** in Kinshasa.
- Across all years and cities, weak-to-strong thermally changed area
  covers **>52% of the study area**, except in Johannesburg, where
  **~51%** of the area shows no change in both 2020 and 2024 — consistent
  with its declining LST trend.

## LULC–UTFVI regression, by city

Standardized linear regression of UTFVI against built-up %, vegetation %,
and water % cover, per city:

| City | Built-up r | Vegetation r | Water | Interpretation |
|---|---|---|---|---|
| Johannesburg | **+0.56** (strongest) | negative (cooling) | negative (cooling) | Strongest urban heat island signal; compact, impervious development style |
| Nairobi | +0.39 | **−0.36** (strongest cooling) | modest cooling | Vegetation and water both meaningfully mitigate heat |
| Lagos | Positive, weaker | Slight negative | Minimal | Weaker slopes, plausibly buffered by coastal moderation and spatial heterogeneity |
| Kinshasa | Slightly positive | Modest cooling | Modest cooling | Weakest relationships overall; complex peri-urban morphology and classification uncertainty |

Built-up expansion is consistently associated with increased thermal
stress across **all four cities**, and vegetation cover shows a
significant cooling effect everywhere. Areas transitioning from
vegetation to built-up land show the sharpest UTFVI increases; gains in
vegetation are linked to more modest UTFVI improvements.

## Spectral index (NDVI/NDBI/MNDWI) corroboration

GAM and Spearman's rank correlation between LST and each spectral index,
fit separately for 2000 and 2024, per city:

- **NDVI** is negatively correlated with LST in nearly every city-year —
  the sole exception is Kinshasa in 2000.
- **NDBI** is positively correlated with LST in **every** city-year
  combination.
- **MNDWI** is generally positively correlated with LST — exceptions are
  Johannesburg and Kinshasa in 2000.
- GAM results are broadly consistent with the Spearman correlations:
  mostly negative NDVI relationships and positive NDBI relationships;
  MNDWI is generally negative under GAM except in 2024.

Selected Spearman's ρ values (2024):

| City | NDBI ρ | NDVI ρ | MNDWI ρ |
|---|---|---|---|
| Johannesburg | 0.402 | −0.404 | 0.032 |
| Kinshasa | 0.604 | −0.670 | 0.290 |
| Lagos | 0.811 | −0.795 | 0.485 |
| Nairobi | 0.813 | −0.789 | 0.452 |

## Headline conclusions

1. **Built-up expansion is the primary, most consistent driver of urban
   thermal stress** across all four SSA cities, confirming impervious
   surface proliferation as the dominant UHI mechanism in this region.
2. **Vegetation cover is the most reliable cooling asset** across diverse
   climatic zones; water bodies provide thermal relief that is smaller
   and more contingent on local landscape configuration.
3. **Despite city-specific differences in magnitude** — shaped by urban
   morphology, governance context, and biophysical setting — the
   *directional* relationships between LULC transitions and thermal
   outcomes are consistent across cities, supporting the generalisability
   of these findings across Sub-Saharan Africa.

## Governance implications

Heat-vulnerable zones systematically correspond with informal settlements
in Lagos and Kinshasa — areas that also lack tree cover, green
infrastructure, and safe housing — positioning urban heat as a **climate
justice issue**, not merely an environmental one. Effective adaptation
must be redistributive, targeting ecological buffers and green
infrastructure investment toward communities bearing a disproportionate
thermal burden. Integrating UTFVI-style metrics into Strategic
Environmental Assessments, building permitting frameworks, and
Nationally Determined Contributions offers a concrete institutional
pathway for embedding spatial thermal intelligence into governance
systems.

## Future directions

The manuscript identifies three priorities for follow-on work:

- Integrating socio-demographic and infrastructural layers to produce
  multi-risk urban vulnerability maps.
- Applying GeoAI and machine-learning techniques — including time-series
  anomaly detection and spatial clustering — to sharpen UHI mapping
  granularity.
- Embedding thermal intelligence into digital urban twins and
  decision-support systems to guide proactive, equitable climate
  planning across SSA's rapidly urbanizing cities.
