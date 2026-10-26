# ASX 200 Market Data Pipeline: Proof of Concept

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/kashishgandhi-hub/market-pipeline/blob/main/asx200_pipeline.ipynb)

A thin slice of the Part 1 implementation plan. It ingests the S&P/ASX 200 (`^AXJO`) from Yahoo Finance end to end: fetch with retries, land raw, validate, split into staging or quarantine, log data quality, then build the curated metrics.

## How to run
Click **Open in Colab** above, then **Runtime > Run all**. It takes about a minute and needs no setup.

## Pipeline layers

| Layer | Where | What it holds |
|---|---|---|
| Raw | `data/raw/source=asx200/load_date=YYYY-MM-DD/` | Response exactly as received, plus a manifest with a sha256 fingerprint. Written before any cleaning, never edited. |
| Staging | DuckDB `staging_prices` | Rows that passed every check. Keyed on (source, date), so re-runs replace instead of duplicating. |
| Quarantine | DuckDB `quarantine` | Failed rows, with the full original record and reason codes. Never deleted. |
| DQ log | DuckDB `dq_runs` | One row per run: status, attempts, rows received, passed, quarantined, warnings, fingerprint. |
| Curated | DuckDB view `curated_asx200_metrics` | Daily log return, 30-day annualised volatility, drawdown from peak. |

Storage (raw files) is kept separate from transformation (DuckDB), so every table can be rebuilt from raw.

## Failure handling

| Scenario | How it's handled |
|---|---|
| API timeout or empty response | Retry 4 times with exponential backoff (1s, 2s, 4s), then stop loudly |
| Schema change (e.g. `Close` renamed) | Data contract check catches it by name before any cleaning |
| Bad rows | Quarantined with a reason code. Rows in = rows passed + rows quarantined, asserted every run |
| Suspicious but valid data | Logged as a warning, row kept |

## Reason codes

| Code | Meaning |
|---|---|
| BAD_DATE | Date missing or unreadable |
| NULL_PRICE | A price is missing |
| NON_POSITIVE_PRICE | A price is zero or negative |
| HIGH_LT_LOW | High below low, an impossible bar |
| NEGATIVE_VOLUME | Volume below zero |
| DUPLICATE_DATE | Same date twice in one batch, first copy kept |
| ZERO_VOLUME (warning) | Volume reported as 0, row kept |

## Findings from real data
- **Zero volume:** Yahoo reported 0 volume on 5 of 715 trading days. Prices on those days were valid, so they are flagged as warnings rather than quarantined. Our metrics only use the close.
- **Provisional latest day:** the most recent day's volume was still 0 when pulled the same evening. Production would re-pull the last 10 days on each run so late corrections are picked up.
- **Worst drawdown:** -14.2% on 7 April 2025, during the tariff sell-off.

## Issues caught in AI-generated code
1. **Silent failure.** yfinance does not raise an error when a download fails. It returns an empty table, so a normal try/except retry never fires. Fixed by treating an empty result as an error.
2. **MultiIndex columns.** Newer yfinance returns two stacked header rows even for one ticker. Fixed with `multi_level_index=False`.
3. **Changed default.** `auto_adjust` now defaults to True, silently adjusting "Close". Fixed by setting `auto_adjust=False` explicitly.

## Tools
Python, Google Colab, yfinance 1.7.0, DuckDB 2.0.0, pandas, matplotlib.
