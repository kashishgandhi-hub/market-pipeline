# ASX 200 Market Intelligence Dashboard — Part 3

## Overview

This Power BI dashboard is a proof of concept for the Really Big Bank market intelligence analytics project. The aim of this dashboard is to help monitor recent ASX 200 market movements, identify changes in volatility, and compare market trends with the Reserve Bank of Australia's (RBA) cash rate.

The analytical findings in this dashboard are based on the most recent 90 days and presented through interactive charts, key indicators, and short explanations.

## Data Sources

**1. ASX 200 Closing Prices — Yahoo Finance**

The ASX 200 data is processed through the group's Python data-curation pipeline from Part 1 and Part 2 of the assessment. The curated dataset contains daily closing prices, logarithmic returns, 30-day annualised volatility, and drawdown values.

**2. RBA Cash Rate Target — Reserve Bank of Australia**

The interest rate data comes from the RBA's official Statistical Table F1 (https://www.rba.gov.au/statistics/cash-rate/). The data was imported through Microsoft Excel and then cleaned using Power BI's Power Query by removing unnecessary metadata, assigning appropriate data types to dates and interest rates, and excluding missing rate values.

## Dashboard Features

### ASX 200 Closing Price and 20-Day Moving Average

A line chart displays ASX 200 daily closing prices alongside a 20-day moving average. The moving average smooths daily fluctuations and helps analysts identify the broader short-term market trend.

### Market Risk Indicator

A traffic-light indicator categorises volatility using the following proposed thresholds:

- **GREEN:** Below 10% — relatively low volatility
- **AMBER:** 10% to 20% — elevated volatility
- **RED:** Above 20% — high volatility

These are the proposed thresholds to monitor the 30-day annualised volatility. The benchmarks were evaluated by observing the amount of days when the volatility were classified as green, amber, and red.

### RBA Cash Rate Comparison

A separate line chart displays the RBA cash rate target over the same 90-day reporting period. This allows analysts to examine monetary policy changes alongside ASX 200 price movements.

Separate vertical scales are used because ASX 200 prices are measured in index points, while interest rates are measured in percentages. Observed movements do not establish a causal relationship.

## How to Use the Dashboard

1. Download the Power BI `.pbix` file from this folder.
2. Open it using Microsoft Power BI Desktop.
3. Review the ASX 200 trend chart, volatility KPI, and market risk indicator.
4. Compare the ASX 200 movements with the RBA cash rate chart.
5. Hover over chart points to inspect individual dates and values.

The report uses a rolling 90-day date filter. Refreshing the underlying datasets requires access to the source files and relevant data-preparation steps.

## Current Proof-of-Concept Findings

At the time of development, the dashboard showed a decline in the ASX 200 from late August into early October 2026, followed by a recovery towards the end of October.

The latest available 30-day annualised volatility was approximately **9.63%**, producing a **GREEN** status under the proposed monitoring thresholds.

The RBA dataset displayed a cash rate increase from **4.35% to 4.60%** during the reporting period. This provides additional market context but should not be interpreted as evidence that the interest rate movement caused the ASX 200 price changes.

## Limitations

- The dashboard is a proof of concept rather than a production monitoring system.
- Risk thresholds are illustrative and would require validation before operational use.
- The 90-day reporting window depends on the available source data and refresh process.
- The dashboard describes historical patterns and does not forecast market performance.
