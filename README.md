# Empirical Analysis MongoDB vs. Apache Hive — Physical Design, Performance and Energy Consumption

Data-analysis notebook for the experimental study comparing **MongoDB 8.0.17** (document-oriented NoSQL) and **Apache Hive 3.1.3** (data warehouse on Hadoop) under the **TPC-H benchmark (SF = 5 GB)**, measuring **execution time** and **energy consumption** (via Scaphandre / Intel RAPL) for four physical-design configurations (*baseline*, indexes, Snappy compression, and indexes + compression) in **distributed** and **centralized** deployment modes.

This repository contains the notebook (`Mongo_Hive.ipynb`) that loads the raw metrics, cleans them, computes statistics and inferential tests, and generates all the figures of the thesis report.

## What the notebook does

From the metrics collected in the experiments (power and time CSVs per query, configuration, iteration and node), the notebook:

- Loads and validates the data (QA), discarding incomplete files.
- Computes energy by **trapezoidal integration** over the power series.
- Applies the **3-hour proportional cap** (10,800 s) to treat *timeouts* as censored observations.
- Aggregates on two levels: mean per query (Q1–Q22) and median across queries per configuration.
- Runs **Spearman correlation** (time–energy) and a **paired Wilcoxon test** with **Holm–Bonferroni** correction (α = 0.05).
- Builds the **ranking of configurations** by significant wins/losses.
- Generates the visualizations: per-query bar charts (log scale), distributed vs. centralized comparisons, time–energy scatter plots, per-node/shard load distribution, heatmaps and radar charts.

## Notebook structure

1. **Setup** — automatically clones the 4 data repositories.
2. **Imports and global configuration** — libraries and constants (timeout, scenarios, CSV pattern).
3. **Helper functions** — helpers for Hive (3.1) and MongoDB (3.2).
4. **Hive analysis** — loading and QA (4.1); statistics and tables (4.2).
5. **Mongo analysis** — loading and QA (5.1); statistics and tables (5.2).
6. **Hive charts**.
7. **Mongo charts**.
8. **Wilcoxon test — Hive**.
9. **Wilcoxon test — Mongo**.
10. **Radar charts** — multidimensional comparison (5 normalized axes).
11. **Per-query radar** — execution time on 22 axes (variants 11.b–11.d).

Suffix convention: `_H` = Hive · `_M` = MongoDB.

## Data

The first cell (**Setup**) clones the four repositories with the raw metrics into the notebook folder, so there is no need to download them manually:

| Engine      | Mode        | Repository                                               |
| ----------- | ----------- | -------------------------------------------------------- |
| Apache Hive | Distributed | <https://github.com/Carrillo-Ch12/Hive_metricas_new_5G>  |
| Apache Hive | Centralized | <https://github.com/Carrillo-Ch12/5g_hive_centralizado>  |
| MongoDB     | Distributed | <https://github.com/Carrillo-Ch12/Metricas_5g>           |
| MongoDB     | Centralized | <https://github.com/Carrillo-Ch12/Mongo_centralizado_5G> |

Each CSV contains, among others, the columns `row_type`, `iteration`, `elapsed_seconds` and `power_total_watts`.

## Requirements

- Python 3.10 or higher
- `git` available on the `PATH` (used by the Setup cell)
- Packages: `numpy`, `pandas`, `matplotlib`, `seaborn`, `scipy`, `jupyter`

Quick install:

```
pip install numpy pandas matplotlib seaborn scipy jupyter
```

## How to run

```
git clone <URL-of-this-repository>
cd Mongo-Hive-Analisis
pip install numpy pandas matplotlib seaborn scipy jupyter
jupyter notebook Mongo_Hive.ipynb
```

Run the cells in order (the Setup cell clones the data on the first run). The notebook is published **without outputs** to keep it lightweight; the figures and tables are regenerated when it is run from start to finish.

## Generated outputs

When executed, the notebook produces the figures used in the report, among them the per-query time and energy bar charts, the distributed vs. centralized comparisons, the time–energy scatter plots, the heatmaps and the radar charts (multidimensional and per-query).

## Relationship to the thesis

This notebook is the reproducible backbone of the **Results** chapter of the work *"Empirical Study of Physical Database Design and its Impact on Efficiency and Energy Consumption"*. The means, Wilcoxon rankings and Spearman correlations reported in the paper are obtained directly from running this notebook.
