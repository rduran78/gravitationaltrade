# Geography of International Trade  
**Network Structure, Spatial Patterns, and Trade Barycenters**

This repository contains the full empirical pipeline for a descriptive analysis of the geography of international trade. International trade is modeled as a global, weighted, directed network in which countries and territories are nodes and bilateral trade flows are edges. The analytical focus is on spatial structure, its evolution over time, and heterogeneity across countries and products.

The analysis is **descriptive and non-causal**. Objects produced here—barycenters, distances, Moran’s I statistics, and clustering outputs—are spatial summaries and pattern-detection tools, not structural parameters or policy counterfactuals.

---

## Data foundations (canonical)

### Trade data

Bilateral trade flows are drawn from the Harvard Growth Lab International Trade Datasets (Bustos, Jackson, Torun et al., 2026). These data reconcile discrepancies across reporting sources and provide a consistent historical panel of directional trade flows.

- One file per year (1976–2023)
- Central variable: `value_final`
- `value_final` represents reconciled **exports** from exporter to importer

Accounting identities:

- Total exports of country A in year *t*:  
  sum(`value_final` | exporter = A)
- Total imports of country A in year *t*:  
  sum(`value_final` | importer = A)

Exports and imports are distinct country-level objects and should not be expected to coincide except at the global level.

### Geographic data

- Country polygons: Natural Earth Admin-0 (1:110m)
- Country locations: fixed geographic centroids (WGS84)
- Distances: great-circle (haversine), symmetric and time-invariant

Geography is treated as a fixed backbone and is not re-estimated downstream.

---

## Repository structure and execution order

The notebooks are designed to be run **sequentially**. Later notebooks assume the outputs of earlier ones exist and are correct.

---

## Notebooks


### `00_Trade_Pipeline_Consolidado.ipynb`  
**Pipeline consolidado y operable (01→05)**

Notebook único de operación que consolida las etapas 01–05 con configuración centralizada de rutas, logging en archivo y consola, barras de progreso, y outputs ordenados por etapa. Usa un único `BASE_DIR` (`C:\Python\trade`) y evita hardcodear subrutas internas.

Incluye:
- Backbone geográfico (centroides + OD)
- Validación de comercio
- Barycenters esféricos
- Clustering gravitacional de trayectorias
- Moran’s I agregado por año

---

### `01 Centroids and OD Matrix.ipynb`  
**Geographic backbone construction**

Constructs the definitive set of geographic centroids for all countries and historical jurisdictions used in the project and builds the full origin–destination distance matrix.

Key outputs:
- Country centroid database
- Symmetric OD distance matrix (great-circle distances)

Centroids are purely geographic, not economically weighted.

---

### `02_Data_Validation_S2_1976_2023.ipynb`  
**Trade data validation and audit**

Validates internal consistency of the reconciled trade data.

Checks include:
- Directionality of `value_final`
- Export and import aggregation identities
- Country coverage and code alignment across years

This notebook is diagnostic and guards against silent data errors.

---

### `03 Barycenter.ipynb`  
**Construction of trade barycenters**

Computes export and import trade barycenters for each country and year using spherical geometry.

Method:
- Partner centroids mapped to unit vectors on the sphere
- Barycenters computed as normalized, value-weighted averages
- Results stored in latitude–longitude form for interpretation

Key outputs:
- `barycenter_{ISO}_1977_2022.csv` (one file per country)

Barycenters capture geographic **orientation**, not trade scale.

---

### `04 Gravitational Clustering.ipynb`  
**Trajectory-based clustering of trade reorientation**

Clusters countries based on the similarity of their **entire barycenter trajectories**, not static locations.

Distance components:
- Positional similarity (long-run co-location)
- Directional similarity (co-movement)

Clustering uses hierarchical agglomerative methods with precomputed distances.

A dominant global cluster is an economically meaningful result, not a methodological failure.

---

### `05 Moran_I_S2_1976_2023.ipynb`  
**Spatial autocorrelation analysis**

Estimates Moran’s I for exports and imports at:
- Aggregate level
- SITC-2 and SITC-3 product levels

Spatial weights:
- Inverse-distance, row-standardized
- Fixed geographic matrix used for all years and sectors

Inference is interpreted descriptively, not as formal hypothesis testing.

---

### `07 Average Distance.ipynb`  
**Average distance of trade**

Computes distance-weighted average trade distances at:
- Global level
- Country–year level
- Product level (SITC-2, SITC-3)

Key identity:
- Global export and import distances coincide by construction
- Country-level export and import distances differ and are meaningful

---

### `08 Speed and Drift.ipynb`  
**Dynamics of trade reorientation**

Constructs kinematic summaries of barycenter movement for each country:

- Average speed (km/year)
- East–West drift index
- Directional instability (switching) index

These indices summarize magnitude, direction, and temporal coherence of trade reorientation.

---

## Methodological stance and limitations

- Entire analysis is descriptive and geometric
- No causal inference or gravity estimation
- Geographic centroids are fixed over time
- Clustering operates in an absolute global frame and is not designed to detect nested hub-and-satellite structures without further transformation

---

## Usage notes

1. Run notebooks in numerical order.
2. Treat intermediate CSV outputs as core research objects.
3. Do not recompute centroids or distances unless explicitly redesigning the geographic backbone.
4. Interpret all outputs as spatial summaries of a global trade network evolving over time.

---

## Reference

Bustos, S., Jackson, E., Torun, D. et al. (2026).  
*Tackling Discrepancies in Trade Data: The Harvard Growth Lab International Trade Datasets*.  
Scientific Data.
