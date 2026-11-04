# Market Analysis and Stock Segmentation Using RFM

6BUIS017W CRM and CM with Business Intelligence, individual coursework, University of Westminster.

This repository segments S&P 500 shares in two ways and compares the results:

1. **Risk and return:** mean weekly return and weekly beta against the S&P 500 (`^GSPC`).
2. **Trading activity:** Recency, Frequency and Monetary (RFM) measures, adapted from customer analytics to share trading volume.

Each feature matrix is clustered with Ward agglomerative clustering and K means, followed by a sensitivity analysis that removes Monetary.

## How to run

1. Open `rfm_coursework_analysis.ipynb` in Google Colab.
2. Upload the `data/` folder from this repository into the Colab session (keep the folder name).
3. Click **Runtime > Run all**.

The notebook loads the saved data snapshot in `data/`, so it produces identical results every run. If you delete the files in `data/`, the notebook downloads fresh data instead, which changes the retrieval dates and possibly the results.

## Study design

| Item | Value |
|---|---|
| Constituent source | Wikipedia, List of S&P 500 companies (retrieval date in `data/run_config.json`) |
| Price source | Yahoo Finance via `yfinance`, `auto_adjust=False`, `actions=True` |
| Study period | 1 January 2021 to 1 January 2025, end date exclusive |
| Benchmark | `^GSPC` (S&P 500 price index) |
| Inclusion rule | Full, clean coverage of the whole study period (see `data/exclusion_log.csv`) |
| Weekly returns | Friday-ending weeks, last available Adj Close; incomplete first and final calendar weeks excluded |
| RFM observation date | Final common completed trading date (`data/run_config.json`) |
| RFM window | Latest 60 trading sessions; high volume day means volume above 2x the mean of the preceding 20 sessions |
| Clustering | Ward (Euclidean, standardised features) and K means (`random_state=42`, `n_init=50`), k from 2 to 8 |

## Repository contents

| Path | Contents |
|---|---|
| `rfm_coursework_analysis.ipynb` | Full analysis: Tasks 1, 2 and the evidence for Task 3 |
| `requirements.txt` | Package versions used |
| `data/sp500_wikipedia_snapshot.html` | Raw Wikipedia page at retrieval |
| `data/sp500_constituents.csv` | Constituent list with retrieval timestamp and GICS sectors |
| `data/prices_raw.parquet`, `data/gspc_raw.parquet` | Daily prices exactly as downloaded |
| `data/acquisition_metadata.json`, `data/package_versions.json` | Provenance |
| `data/exclusion_log.csv` | Every constituent, every validation check, the inclusion decision and the reason |
| `data/prices_validated.parquet`, `data/gspc_validated.parquet` | Validated input to the feature calculations |
| `data/feature_table.csv` | One row per included share: return, beta, R, F, M |
| `data/cluster_assignments.csv` | Cluster assignments for the four baseline solutions and the R and F only solution |
| `data/run_config.json` | Study dates, observation date and share counts |
| `outputs/` | Every figure (PNG) and table (CSV) used in the report, plus `results_summary.json` |

## Limitations

* The constituent list is the current index membership applied back to 2021, which introduces survivorship bias.
* `^GSPC` is a price index, while share returns use dividend-adjusted prices.
* Monetary (Close x Volume) approximates traded value and is a liquidity proxy only. It does not measure spreads, price impact, market capitalisation or profit.
* The clusters describe 2021 to 2024 behaviour and are not forecasts of future returns or risk.
