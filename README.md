# 3D Digital Twin & Solar Yield Predictive Pipeline (Case Study: University of Ibadan)

This project provides an end-to-end automated pipeline for extracting 3D building stock footprints, simulating micro-climate shading impacts (including surrounding tropical tree canopies), and building physics-informed predictive machine learning models to forecast annual and hourly solar energy generation potential for the University of Ibadan campus.

## Project Architecture & Modules

### Phase 1: Base Map & Elevation Extraction
* **Module 1**: Downloads building footprints within a 1.5km radius from OpenStreetMap using `OSMNX` and samples ground elevations using SRTM 30m digital elevation models (DEM) with local GeoTIFF and remote API fallbacks.
* **Module 2**: Performs exploratory data analysis, filtering out spatial noise (structures < 10 m²) and profiling campus ground elevation distribution.

### Phase 2: 3D Extrusion & Base Solar Yield
* **Module 3 & 4**: Establishes structural heights using semantic height models based on institutional vs. residential classification. Extrudes 2D polygon vectors into true 3D PolygonZ roof planes.
* **Module 5**: Implements an interactive 3D WebGL map using `PyDeck` over CARTO Dark map styles to display building structures color-coded by height.
* **Module 6**: Models solar transposition physics using monthly GHI/DHI climatology matrices, factoring in the unique dry-season Harmattan dust soiling losses.

### Phase 3: Dynamic Shading & Predictive Modeling
* **Module 7**: Calculates hourly solar azimuths, altitude angles, and worst-case shadow length vectors during the Winter Solstice.
* **Module 8**: Generates a procedural 12-meter tropical tree canopy buffer around structures and runs a 2.5D vector shadow casting simulation using spatial R-tree intersections to map precise hourly building occlusions.
* **Module 9**: Feeds real historical ERA5 climate reanalysis parameters into an optimized **LightGBM GOSS (Gradient-Based One-Side Sampling)** machine learning regressor. It maps complex micro-climate variables (panel overheating, wind speeds, dynamic shading, and soiling) to predict net power output, achieving an $R^2$ score of **0.999**.
* **Validation**: Compares the LightGBM regressor to Random Forest, and Linear regression baselines, followed by a strict 5-fold temporal cross-validation to guarantee zero seasonal data leakage.

### Phase 4: Spatial Context Identification & Interactive Dashboard
* **Identification Pipeline**: Resolves OSM unnamed buildings by calculating nearest-neighbor distances to campus roads and named landmarks (such as faculties, libraries, and halls of residence), generating highly descriptive smart labels.
* **Interactive 3D Twin**: Compiles an interactive 3D Pydeck WebGL visualizer integrating custom smart labels, latitude/longitude coordinates, shading severities, and heights into interactive map tooltips.
