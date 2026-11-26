# Urban Pluvial Flood Hazard and Exposure under CMIP6-Adjusted Design Storms in Khulna, Bangladesh
 
Python code for the rainfall frequency analysis, climate-change adjustment, design storms, building parameters, Urban Flood Hazard Index (UFHI) and exposure analysis reported in:
 
> Mabrook, Sarkar SK (2026) Urban Pluvial Flood Hazard and Exposure under CMIP6-Adjusted Design Storms in Khulna, Bangladesh. 
 
The study covers Wards 27, 28, 30 and 31 of the Khulna City Corporation. Design rainfall from the gauge record (1961 to 2019) is adjusted for future climate with 13 bias-corrected CMIP6 models under SSP2-4.5 and SSP5-8.5, and drives a coupled 1D-2D HEC-RAS 7.0.1 model for four return periods under five climates (20 simulations). The simulated depth and velocity are combined into the UFHI, and the classified hazard maps are overlaid on buildings, roads and critical facilities.
 
The hydrodynamic simulations and the maps were produced in HEC-RAS and ArcGIS Pro and are not part of this repository. The notebooks cover every step before the simulations (notebooks 01 to 06) and after them (notebooks 07 to 09).
 
## Repository structure
 
```
├── README.md
├── LICENSE
├── CITATION.cff
├── requirements.txt
└── notebooks/             the nine notebooks, run in numerical order
```
 
## Workflow
 
| Notebook | Purpose |
|:--|:--|
| `01_rainfall_trend_analysis` | Annual maximum series; Durbin-Watson, lag-1 autocorrelation, Mann-Kendall test and Sen slope |
| `02_distribution_fitting` | Gumbel, GEV, Log-Pearson III and lognormal fits; goodness of fit; observed design rainfall |
| `03_cmip6_delta_change` | Annual maxima of 13 CMIP6 models; ensemble-mean series; change factors; future design rainfall |
| `04_idf_curves` | One-third rule, IDF curves and Sherman equation for the five climates |
| `05_design_hyetographs` | 24-h design hyetographs by the alternating block method for the five climates |
| `06_building_porosity_drag` | Building fraction, porosity, frontal-area density and quadratic drag by building-density class |
| (HEC-RAS 7.0.1) | 20 coupled 1D-2D simulations; export of maximum depth and velocity rasters |
| `07_urban_flood_hazard_index` | Waterbody mask, momentum, pooled normalisation, entropy weights, UFHI, Jenks classes, class areas |
| `08_exposure_and_expected_annual_exposure` | Exposure of buildings, roads and critical facilities; frequency amplification; expected annual exposure; ward ranking |
| `09_cmip6_diagnostics_and_hazard_distributions` | Anomaly series and violin plots of the projections; sensitivity of the 100-year change factor; wet-cell statistics |
 
Notebooks 01 to 05 form one chain, and each reads the outputs of those before it. Notebook 06 needs only the building footprints. Notebooks 07 to 09 need the rasters exported from HEC-RAS; notebook 09 also reads the outputs of notebooks 01, 03 and 07.
 
## Requirements
 
Python 3.11 and the packages in `requirements.txt` (NumPy, pandas, SciPy, Matplotlib, GeoPandas, Shapely, Rasterio, rasterstats, jenkspy, openpyxl).
 
```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```
 
## Input data
 
The notebooks read their inputs from a `data/` folder in the repository root, with the following layout. All spatial layers are in WGS 1984 UTM Zone 45N (EPSG:32645).
 
| Path | Used by | Content |
|:--|:--|:--|
| `data/rainfall/khulna_CL510_daily.csv` | 01 | Daily rainfall at Khulna, 1961 to 2019 (columns `Date` and rainfall in mm) |
| `data/cmip6/<MODEL>/<scenario>/PrecipData*` | 03, 09 | Daily precipitation of 13 bias-corrected CMIP6 models; scenario `historical`, `ssp245` or `ssp585` |
| `data/buildings/building_footprints_part1.shp`, `building_footprints_part2.shp` | 06 | Building footprints covering the 2D model domain |
| `data/buildings/buildings_study_wards.shp` | 08 | Building footprints of the four wards with fields `Struse1T`, `Struse2T`, `STRUCTYPE` and `WARD_NO` |
| `data/boundaries/model_domain.shp` | 06 | Polygon of the 2D model domain |
| `data/waterbodies/waterbodies.shp` | 07 | Polygons of the mapped ponds and khals |
| `data/roads/<type>.shp` | 08 | Road-surface polygons: `Pacca`, `Kacharoad`, `Island`, `Footpath`, `Semipacca` |
| `data/hecras/depth_<rp>_<climate>.tif`, `velocity_<rp>_<climate>.tif` | 07 | Maximum depth (m) and velocity (m s⁻¹) of the 20 simulations |
 
In the raster names, `<rp>` is one of `2yr`, `10yr`, `50yr`, `100yr` and `<climate>` one of `Observed`, `SSP245_2021_2050`, `SSP245_2051_2080`, `SSP585_2021_2050`, `SSP585_2051_2080`.
 
## How to run
 
1. Place the input data in `data/` with the file names given above.
2. Open the notebooks from the `notebooks/` folder and run them in numerical order, each from top to bottom. All paths are relative to the repository root, and outputs are written to `results/`.
3. Notebooks 07 to 09 require the maximum depth and velocity rasters of the 20 HEC-RAS simulations in `data/hecras/`.
Notebook 07 holds the depth, velocity and momentum arrays of all 20 cases in memory to pool them; its memory use scales with the raster size and reaches several gigabytes for 1 m rasters of this extent.
 
## Notes on reproducibility
 
- The Jenks class breaks are computed from a random sample of 150,000 pooled index values drawn with a fixed seed (`numpy.random.default_rng(0)`), so they are reproducible for a given NumPy version.
- The Sherman coefficients are found by non-linear least squares; the coefficient *k* can differ in the fifth significant figure between SciPy versions.
- The ensemble-mean annual maxima are rounded to 0.01 mm before the GEV fit in notebook 03, and design depths are rounded to 0.1 mm before they are passed to notebooks 04 and 05.
## Data availability
 
The input data are not distributed with this repository. The gauge record belongs to the Bangladesh Water Development Board, and the terrain, drainage, building and road layers to the Khulna City Corporation and the Khulna Development Authority; these can be requested from the providers. The bias-corrected CMIP6 projections are described by Mishra et al. (2020, https://doi.org/10.1038/s41597-020-00681-1). The maximum depth and velocity rasters of the 20 simulations, with UFHI rasters are available at https://doi.org/10.5281/zenodo.24246162
 
## Licence
 
The code is released under the MIT Licence (see `LICENSE`).
 
## Citation
 
If you use this code, please cite the article above and the archived release of this repository:
 
> Mabrook, Sarkar SK (2026) Code for: Urban Pluvial Flood Hazard and Exposure under CMIP6-Adjusted Design Storms in Khulna, Bangladesh (version 1.0.0). Zenodo. https://doi.org/10.5281/zenodo.24561719
 
## Contact
 
Mabrook (corresponding author), Department of Urban and Regional Planning, Khulna University of Engineering & Technology, Khulna, Bangladesh. [mkmabrook7@gmail.com]
