# TFM Code: A Predictive Model of Gentrification in Madrid Neighbourhoods

This holds the code for my Master's Thesis (TFM) at Universidad Carlos III de Madrid (UC3M), which builds a barrio-level gentrification index for Madrid (2017-2023, adapting the index methodology of Sanz-Pérez et al. (2026) and its López-Gay et al. (2025) precedent) and uses it to train a predictive model that projects gentrification degree forward to 2025.

## Repository structure

```
TFM_PROJECT/
├── TFM_PROJECT.Rproj
├── README.md
├── scripts/
│   ├── 01_union_datos_ingles.qmd      # 1. builds the neighbourhood-year panel and dataset_modelado
│   └── 02_analisis_datos_ingles.qmd   # 2. index, PCA/clustering validation, walk-forward modelling with RF and XGBoost, SHAP and 2025 prediction, figures & tables
├── data/
│   ├── raw/                           # source files, already included 
│   └── processed/                     # every intermediate table + dataset_modelado.csv, already included
└── outputs/
    ├── figures/                       # PNG/PDF figures used in the thesis
    └── tables/                        # HTML tables used in the thesis
```

## 1. Get the data

Data (`data/raw/` and `data/processed/`) ship directly inside this project folder, so there is no external download step.
`data/raw/` holds the original source files collected from public repositories: Banco de datos, Portal de Datos Abiertos, Geoportal and Portal Web all provided by Ayuntamiento de Madrid and Portal del Consorcio de Transportes de Madrid.
They should be treated as immutable inputs. 


## 2. Set up the R environment

Requires R (a recent 4.x release) and the system libraries GDAL, GEOS and PROJ that the `sf` package (used in both scripts) depends on. 

There is no `renv` lockfile yet; packages are loaded via `library()` calls at the top of each script. The main dependencies are:

- Script 1 (`01_union_datos_ingles.qmd`): `tidyverse`, `readxl`, `igraph`, `sf`
- Script 2 (`02_analisis_datos_ingles.qmd`): `tidyverse`, `naniar`, `sf`, `patchwork`, `stargazer`, `randomForest`, `xgboost`, `psych`, `fastshap`, `ggbeeswarm`, `factoextra`, `showtext`

## 3. Run the scripts

`scripts/01_union_datos_ingles.qmd` reads the 17 raw sources, builds the 131-neighbourhood × 7-year panel, and writes the final modelling table to `data/processed/dataset_modelado.csv`.
`scripts/02_analisis_datos_ingles.qmd` builds the gentrification index from that table, validates it (PCA, hierarchical clustering), trains and walk-forward-validates the predictive models (Random Forest and XGBoosting), and produces the 2025 prediction, interpreting the SHAP values and creating figures and tables.

- **Full reproduction**: run `01_union_datos_ingles.qmd` first, then `02_analisis_datos_ingles.qmd`.
- **Skip script 1**: `data/processed/` already ships with `dataset_modelado.csv` (and every other intermediate table), and `02_analisis_datos_ingles.qmd` reads it fresh from disk (`read_csv("../data/processed/dataset_modelado.csv")`), so you can run `02_analisis_datos_ingles.qmd` directly, without ever running script 1, as long as `data/processed/` is present.

## 4. Notes: 

- `outputs/figures/` and `outputs/tables/` already ship pre-generated (PNG/PDF figures and HTML tables); re-running `02_analisis_datos_ingles.qmd` regenerates them in place.
- `data/processed/modelo_final_objetos.rds` stores the final fitted model objects (the winning model, retrained on the full panel) used for the 2023 → 2025 prediction.
- Main references: 

Sanz-Pérez, C., López-Gay, A., & Valente, R.  (2026). The spread of gentrification in Spain: comparative analysis in ten cities through a multidimensional index. Boletín de la Asociación de Geógrafos Españoles, (107). https://doi.org/10.21138/bage.3733


López Gay, A., Sales Favà, J., Solana Solana, A. M., Fernández Sánchez, A., & Peralta Chiriboga, A. A. (2019). Midiendo los procesos de gentrificación en Barcelona y Madrid: una propuesta metodológica. Congreso internacional de Ciudad y Territorio Virtual (CTV). https://doi.org/10.5821/ctv.8680

