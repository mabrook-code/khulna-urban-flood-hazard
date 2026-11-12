# Climate-adjusted urban flood hazard and exposure in Khulna City, Bangladesh

Python code for the rainfall frequency analysis, climate-change adjustment, design storms, building parameters, Urban Flood Hazard Index (UFHI) and exposure analysis reported in:

> [AUTHORS] ([YEAR]) [ARTICLE TITLE]. [JOURNAL]. https://doi.org/[ARTICLE DOI]

The study covers Wards 27, 28, 30 and 31 of the Khulna City Corporation. Design rainfall from the gauge record (1961 to 2019) is adjusted for future climate with 13 bias-corrected CMIP6 models under SSP2-4.5 and SSP5-8.5, and drives a coupled 1D-2D HEC-RAS 7.0.1 model for four return periods under five climates (20 simulations). The simulated depth and velocity are combined into the UFHI, and the classified hazard maps are overlaid on buildings, roads and critical facilities.

The hydrodynamic simulations and the maps were produced in HEC-RAS and ArcGIS Pro and are not part of this repository. The notebooks cover every step before the simulations (notebooks 01 to 06) and after them (notebooks 07 to 09).

## Repository structure

```
├── README.md
├── LICENSE
├── CITATION.cff
├── requirements.txt
├── data/
│   └── README.md          input files expected by the notebooks and their sources
├── notebooks/             the nine notebooks, run in numerical order
└── results/
    └── README.md          layout of the output folders
```

## Workflow

| Notebook | Purpose | Manuscript section |
|:--|:--|:--|
| `01_rainfall_trend_analysis` | Annual maximum series; Durbin-Watson, lag-1 autocorrelation, Mann-Kendall test and Sen slope | 2.2.1, 3.1 |
| `02_distribution_fitting` | Gumbel, GEV, Log-Pearson III and lognormal fits; goodness of fit; observed design rainfall | 2.2.2, 3.2 |
| `03_cmip6_delta_change` | Annual maxima of 13 CMIP6 models; ensemble-mean series; change factors; future design rainfall | 2.3.1, 2.3.2, 3.2 |
| `04_idf_curves` | One-third rule, IDF curves and Sherman equation for the five climates | 2.2.3, 3.2 |
| `05_design_hyetographs` | 24-h design hyetographs by the alternating block method for the five climates | 2.4, 3.2 |
| `06_building_porosity_drag` | Building fraction, porosity, frontal-area density and quadratic drag by building-density class | 2.5.4 |
| (HEC-RAS 7.0.1) | 20 coupled 1D-2D simulations; export of maximum depth and velocity rasters | 2.5 |
| `07_urban_flood_hazard_index` | Waterbody mask, momentum, pooled normalisation, entropy weights, UFHI, Jenks classes, class areas | 2.5.5, 2.6.1, 2.6.2, 3.3 to 3.5 |
| `08_exposure_and_expected_annual_exposure` | Exposure of buildings, roads and critical facilities; frequency amplification; expected annual exposure; ward ranking | 2.7, 3.7, 3.8 |
| `09_cmip6_diagnostics_and_hazard_distributions` | Anomaly series and violin plots of the projections; sensitivity of the 100-year change factor; wet-cell statistics | 2.3.3, 2.6.3, 3.1, 3.2, 3.6 |

Notebooks 01 to 05 form one chain, and each reads the outputs of those before it. Notebook 06 needs only the building footprints. Notebooks 07 to 09 need the rasters exported from HEC-RAS; notebook 09 also reads the outputs of notebooks 01, 03 and 07.

## Tables and figures of the manuscript

Numbering follows the submitted manuscript.

| Item | Notebook | Output |
|:--|:--|:--|
| Table 3 | 06 | `results/06_porosity_drag/porosity_drag_class_parameters.csv` |
| Table 4, Fig. A.2 | 02 | `goodness_of_fit.csv`, `fig_distribution_comparison.png` |
| Table 5 | 02, 03 | `results/03_cmip6/design_rainfall_all_cases.csv` |
| Fig. 4 (a to d) | 09 | `fig_gauge_ams_timeseries`, `fig_gauge_vs_gcm_violin`, `fig_cmip6_anomaly_timeseries`, `fig_cmip6_anomaly_violins` |
| Fig. A.1 (a to d), Table A.1 | 09 | `fig_per_gcm_violins_<ssp>_<window>`, `cf100_sensitivity.csv` |
| Fig. A.3, Table A.2 | 04 | `<case>/fig_idf_curves.png`, `sherman_parameters.csv` |
| Fig. 5, Fig. A.4, Table A.3 | 05 | `<case>/fig_hyetographs.png`, `peak_intensity.csv` |
| Table 6, Table A.4, values in Section 3.4 | 07 | `flood_summary.csv`, `hazard_class_percentages.csv`, `entropy_weights.csv`, `jenks_breaks.csv`, `class_break_depths.csv` |
| Fig. 8 (a to c), Table A.5 | 09 | `fig_wet_cell_depth`, `fig_wet_cell_velocity`, `fig_wet_cell_ufhi`, `wet_cell_statistics.csv` |
| Fig. 9, Fig. 10 | 08 | `fig_exposure_frequency_*.png`, `fig_usage_heatmap.png` |
| Tables 7, A.6, A.7, A.8, A.9 | 08 | sheets of `exposure_risk_results.xlsx` |
| Fig. 11 | 08 | drawn from the sheets `frequency_amplification`, `EAE` and `ward_EAE`; the notebook plots the same values |

Multi-panel figures were assembled from the single-panel files listed above. Figures 1, 2, 3, 6 and 7 and Figs. A.5 to A.12 are maps and diagrams prepared in ArcGIS Pro; the flood depth and hazard maps display the `depth_masked_*.tif` and `UFHI_classified_*.tif` rasters written by notebook 07, and the building-density map displays `building_density_class_5m.tif` written by notebook 06.

## Requirements

Python 3.11 and the packages in `requirements.txt` (NumPy, pandas, SciPy, Matplotlib, GeoPandas, Shapely, Rasterio, rasterstats, jenkspy, openpyxl).

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```

## How to run

1. Place the input data in `data/` with the file names given in `data/README.md`.
2. Open the notebooks from the `notebooks/` folder and run them in numerical order, each from top to bottom. All paths are relative to the repository root, and outputs are written to `results/`.
3. Notebooks 07 to 09 require the maximum depth and velocity rasters of the 20 HEC-RAS simulations in `data/hecras/`.

Notebook 07 holds the depth, velocity and momentum arrays of all 20 cases in memory to pool them; its memory use scales with the raster size and reaches several gigabytes for 1 m rasters of this extent.

## Notes on reproducibility

- The Jenks class breaks are computed from a random sample of 150,000 pooled index values drawn with a fixed seed (`numpy.random.default_rng(0)`), so they are reproducible for a given NumPy version.
- The Sherman coefficients are found by non-linear least squares; the coefficient *k* can differ in the fifth significant figure between SciPy versions.
- The ensemble-mean annual maxima are rounded to 0.01 mm before the GEV fit in notebook 03, and design depths are rounded to 0.1 mm before they are passed to notebooks 04 and 05.

## Data availability

The input data are not distributed with this repository. The gauge record belongs to the Bangladesh Water Development Board, the terrain, drainage, building and road layers to the Khulna City Corporation and the Khulna Development Authority, and the bias-corrected CMIP6 projections are described by Mishra et al. (2020, https://doi.org/10.1038/s41597-020-00681-1). `data/README.md` lists every file, its format and its source.

## Licence

The code is released under the MIT Licence (see `LICENSE`).

## Citation

If you use this code, please cite the article above and the archived release of this repository:

> [AUTHORS] ([YEAR]) Code for: [ARTICLE TITLE] (version 1.0.0). Zenodo. https://doi.org/[ZENODO DOI]

## Contact

[CORRESPONDING AUTHOR, AFFILIATION, E-MAIL]
