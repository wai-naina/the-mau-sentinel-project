# Eastern Mau Forest Loss Monitoring (2019–2024)

Satellite-based forest-cover change detection for the **Eastern Mau Forest Reserve**, Kenya, using Sentinel-2 imagery on Google Earth Engine. The pipeline establishes a forest baseline, detects canopy loss against it, and reports a conservative, validated loss figure suitable for MRV-style monitoring.

**Headline result:** Of ~27,374 ha of forest standing in 2019, an estimated **325 ha (1.19%)** was lost by 2024 — a mean rate of **0.24%/yr**.

---

## Why this matters

The Mau Forest Complex is Kenya's largest closed-canopy montane forest and a critical "water tower" feeding the Mara, Sondu, and Njoro river systems. The Eastern Mau block has a documented history of excision, settlement, and clearing, which makes repeatable, transparent forest monitoring directly relevant to conservation enforcement, restoration planning, and the baselines and measurement that underpin nature-based carbon projects.

This project is a compact, reproducible demonstration of that kind of monitoring: from an authoritative boundary to a defensible change figure, with the analytical decisions made explicit.

---

## Method

The pipeline is modular — each stage is a single-purpose function, and the region, dates, and thresholds live in one configuration block, so adapting it to another forest block or time period means editing only that block.

1. **Study area.** The Eastern Mau boundary is taken from the World Database on Protected Areas (WDPA, UNEP-WCMC), selected by name rather than an arbitrary bounding box.
2. **Imagery.** Sentinel-2 Surface Reflectance (harmonized collection), composited over the January–March dry season of each year to minimise cloud cover. Clouds and cirrus are masked using the QA60 band, and a per-pixel median composite is taken so transient clouds are rejected.
3. **Forest definition.** Forest is defined as NDVI above a threshold (0.6) on the median composite — a transparent, auditable rule where every classified pixel traces back to a single value. The threshold was validated visually against true-colour imagery before use.
4. **Baseline normalization.** The two yearly composites sat at slightly different NDVI baselines (a +0.047 offset measured over unchanged stable forest). This systematic offset was removed before change detection, so loss is measured on comparable data.
5. **Loss detection.** Loss is defined as established 2019 forest whose NDVI dropped by at least 0.15. Anchoring detection to known-forest pixels makes it robust to baseline drift.
6. **Noise filtering.** A minimum-mapping-unit filter removes change patches smaller than 0.5 ha, since real forest loss is spatially coherent and isolated single-pixel flips are noise.

---

## Results

| Metric | Value |
|---|---|
| Established forest, 2019 | 27,374.1 ha |
| Forest retained to 2024 | 27,049.1 ha |
| Forest lost | 325.0 ha (1.19%) |
| Mean annual loss rate | 0.24 %/yr |
| Analysis period | 2019–2024 (5 years) |

![Forest change map](https://earthengine.googleapis.com/v1/projects/mau-sentinel/thumbnails/9827be190a7a6187c01b98d7094e7a53-891b4aaf5de066514136a73d81f878c3:getPixels)

*Forest loss (2019–2024) within the Eastern Mau Forest Reserve. Retained forest in green, detected loss in red, over a true-colour Sentinel-2 backdrop.*

---

## Validation and limitations

This project deliberately reports only what the data supports, and is explicit about what it does not.

- **Threshold validation.** The NDVI forest threshold was checked against true-colour imagery; non-forest zones inside the reserve boundary (settlement and cultivation in the north) were confirmed visually rather than assumed.
- **Why loss only, no gain.** An initial two-year classification implied implausible forest *gain* (+25%). Investigation showed this was an artefact of inter-annual composite differences — unchanged forest read ~0.047 NDVI higher in 2024, inflating apparent gain. Baseline normalization reduced but did not fully remove this. The analysis was therefore narrowed to **loss of established forest**, which is anchored to known-forest pixels and far less sensitive to this drift. Gain/regrowth is not reported because it cannot be reliably separated from signal differences with this data and method.
- **Conservative by design.** The thresholds (NDVI 0.6, change magnitude 0.15, 0.5 ha minimum patch) capture stand-replacing loss conservatively. Subtle sub-canopy degradation is likely under-counted; the reported figure is best read as a lower bound on stand-clearing loss.
- **Not field-verified.** Results are remote-sensing estimates validated visually, not against ground truth.

**With more time:** a longer annual time series (trend rather than two snapshots), per-pixel relative radiometric normalization, a supervised classifier with training points, and accuracy assessment against reference data.

---

## Reproducing this

```bash
git clone https://github.com/<your-username>/the-mau-sentinel-project.git
cd the-mau-sentinel-project
pip install -r requirements.txt
```

Open `mau_forest_monitoring.ipynb` (Google Colab or Jupyter). You will need a Google Earth Engine account and a registered Cloud project; set your project ID in the configuration cell. Run the cells in order.

---

## Data sources

- **Sentinel-2 Surface Reflectance (harmonized)** — Copernicus / ESA, via Google Earth Engine. Free and open.
- **World Database on Protected Areas (WDPA)** — UNEP-WCMC and IUCN, via Google Earth Engine (`WCMC/WDPA/current/polygons`). Free for non-commercial, research, and educational use; commercial use requires written permission from UNEP-WCMC. Attribution: IUCN and UNEP-WCMC, *The World Database on Protected Areas*, Protected Planet.

---

## Tools

Google Earth Engine (Python API), `geemap`, Sentinel-2, NDVI change detection.
