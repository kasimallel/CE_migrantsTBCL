# TB screening and preventive treatment for international migrants in Chile: cost-effectiveness model

R code for the paper *Tuberculosis screening and preventive treatment for international migrants in Chile: a modelling and cost-effectiveness study* (manuscript in preparation).

Authors: Kasim Allel, Pablo Valenzuela, Maria Jose Hernandez-Leal, Hassan Haghparast-Bidgoli, Dominik Zenner, María Elvira Balcells.

Funding: FONIS 2025 (Chile), *Impacto en salud y costo-efectividad de estrategias preventivas contra la tuberculosis en migrantes en Chile*.

## What the model does

The model compares seven strategies for migrants aged 15 years or older living in Chile:

| Code | Strategy |
|---|---|
| `SQ` | Status quo (passive case finding) |
| `CXR` | Chest x-ray (CXR) screening for active TB |
| `CXR_TST` | CXR + tuberculin skin test, 3HP for latent TB infection (LTBI) |
| `CXR_IGRA` | CXR + interferon-gamma release assay, 3HP for LTBI |
| `ENH_CXR` | `CXR` + uptake-enhancement package |
| `ENH_TST` | `CXR_TST` + uptake-enhancement package |
| `ENH_IGRA` | `CXR_IGRA` + uptake-enhancement package |

It has three parts:

1. A decision tree for screening. CXR comes first, with or without TST or IGRA. Second-line testing is Xpert Ultra or culture, and third-line testing checks for rifampicin resistance.
2. A monthly Markov cohort model with 11 states, run to age 100. It covers LTBI, preventive treatment, active TB, drug-susceptible and rifampicin-resistant treatment, recovery, TB death and other-cause death.
3. A transmission module that turns infectious person-time into secondary infections and cases.

Costs are in Chilean pesos (2025), converted to USD at CLP 950 per USD. The analysis takes the health-system perspective. Costs and outcomes are discounted at 3% a year, and the willingness-to-pay threshold is 1 x GDP per capita (USD 16,700 per QALY).

## Repository structure

```
R/
  00_run_all.R             # runs the full pipeline
  01_parameters.R          # 86 inputs: base value, range, PSA distribution, source;
                           # age bands, origin mix, national roll-out inputs
  02_model_functions.R     # strategies, decision tree, Markov model, transmission,
                           # incremental analysis, PSA sampler
  03_base_case.R           # base case, age and origin subgroups, thresholds, calibration
  04_dsa.R                 # one-way deterministic sensitivity analysis
  05_psa.R                 # probabilistic sensitivity analysis, CEAC/CEAF, EVPI
  06_scenarios.R           # structural and policy scenario analyses
  07_population_impact.R   # national impact projection, 2026-2035
  08_tables_figures.R      # tables (csv + xlsx) and figures (tiff, png, pdf)
```

All inputs are defined in `R/01_parameters.R`, each with its source, so the model needs no external data files.

## Requirements

- R 4.5 or later (developed on R 4.5.1)
- CRAN packages: `ggplot2`, `patchwork`, `scales`, `ggrepel`, `writexl`

```r
install.packages(c("ggplot2", "patchwork", "scales", "ggrepel", "writexl"))
```

The rest uses base R. The PSA and DSA run in parallel through `parallel::mclapply`, which forks processes on macOS and Linux. On Windows `mclapply` runs on a single core, so the scripts still work but take longer.

## How to run

From the repository root:

```bash
Rscript R/00_run_all.R
```

Or from R, with the working directory set to the repository root:

```r
source("R/00_run_all.R")
```

The full run uses 1,000 PSA draws and takes about 5 to 10 minutes on 8 cores. To change the number of draws or cores, edit `N_PSA` and `NCORES` in `R/00_run_all.R`. The PSA seed is set in `draw_params()` in `R/02_model_functions.R`, so results can be reproduced.

## Outputs

The scripts create an `outputs/` folder:

```
outputs/
  base_case.rds, dsa.rds, psa.rds, scenarios.rds, population.rds
  sessionInfo.txt
  tables/    Table1-4 and supplementary tables (.csv), All_tables.xlsx
  figures/   Figures 1-5 and S1-S4 (.tiff 600 dpi, .png 500 dpi, .pdf)
```


## Citation

If you use this code, please cite the paper (reference to be added on publication).

