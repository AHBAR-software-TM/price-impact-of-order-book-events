# The Price Impact of Order Book Events: Cross-Market Replication

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Course](https://img.shields.io/badge/Course-Financial%20Machine%20Learning-green.svg)]()

> **Academic Framework:** Replication and extension of Cont, Kukanov, & Stoikov (2014), *"The Price Impact of Order Book Events"*, *Journal of Financial Econometrics*.

---

## 📌 Executive Overview

This repository contains the full end-to-end Python implementation and empirical analysis replicating the Order Flow Imbalance (OFI) price impact framework by Cont et al. (2014) across two distinct market environments:
1. **Crypto Perpetual Futures:** High-frequency quote/trade data for **BTC, ETH, SOL, HYPE, and DOGE** on Hyperliquid across three sampling dates (May, June, and July 2026).
2. **U.S. Equities:** High-frequency NBBO quote stream (~2.7M events) for **AAPL and AMZN** during a regular US trading session ($09:30 - 16:00$).

---

## 🔑 Key Findings

- **Directional Consistency ($\beta > 0$):** Across **746 half-hour regressions** estimated (720 crypto, 26 stock), **100%** of the estimated main-model OFI coefficients ($\beta$) were positive, with **99.58% statistical significance** ($p < 0.05$) in crypto and **92.31%** in equities.
- **Explanatory Power ($R^2$ Benchmark):**
  - **U.S. Equities:** Produced a mean $R^2$ of **0.5528** (AAPL) and **0.7401** (AMZN), yielding a combined simple average of **0.6464**—nearly identical to the original paper's **~0.65** benchmark.
  - **Crypto Perpetuals:** Achieved an overall mean $R^2$ of **0.4406** across all assets, heavily influenced by quote-sampling density drops in July.
- **Sampling Frequency Effects:** Quote updates per 10s bin dropped from ~12–18 (May–June) to ~1.8 (July) in crypto, causing a sharp drop in mean $R^2$ (~0.52–0.61 down to ~0.25–0.34) due to inter-snapshot micro-structure noise compression.
- **OFI vs. Trade Imbalance ($TI$):** OFI consistently outperforms Trade Imbalance and Trade Volume as a standalone predictor. However, in crypto, $TI$ retains more incremental information than in equities.

---

## 📐 Mathematical Methodology

### 1. Event-Level Order Flow Imbalance ($e_n$)
Converts every individual order book state change into net demand/supply changes at the top of the book:
$$e_n = I_{\{P_n^B \ge P_{n-1}^B\}} q_n^B - I_{\{P_n^B \le P_{n-1}^B\}} q_{n-1}^B - I_{\{P_n^A \le P_{n-1}^A\}} q_n^A + I_{\{P_n^A \ge P_{n-1}^A\}} q_{n-1}^A$$

### 2. Linear Price Impact Model
Calculates 10-second discrete $\text{OFI}_k$ and regress contemporary mid-price changes ($\Delta P_k$, in ticks) over rolling 30-minute intervals:
$$\Delta P_k = \alpha_i + \beta_i \cdot \text{OFI}_k + \epsilon_k$$
*(Models are estimated via OLS using **White HC0** heteroskedasticity-robust standard errors).*

### 3. Price Impact vs. Market Depth Elasticity ($\lambda$)
Tests power-law decay of price impact slope $$\beta_i$$ relative to average depth $$\bar{D}\_i$$:
$$\log(\beta_i) = \alpha_{L,i} - \lambda \log(\bar{D}\_i) + \epsilon\_{L,i}$$

### 4. Non-Linear Price Impact Test
$$\Delta P_k = \alpha_{Q,i} + \beta_{Q,i} \cdot \text{OFI}_k + \gamma_{Q,i} \cdot \text{OFI}_k |\text{OFI}_k | + \epsilon_{Q,k}$$

---


## 🚀 Quickstart & Execution Sequence

### 1. Prerequisites & Setup
```bash
pip install pandas numpy statsmodels scipy matplotlib parquet pyarrow
```
### 2. Repository Structure
The repository is organized with the master analytical notebook, the comprehensive project report, and the raw datasets compressed into multi-part archives.

```text
.
├── Full Report.pdf                        # Detailed academic project report and empirical findings
├── README.md                              # Project documentation and setup instructions
├── price_impact_order_book_events.ipynb   # Master replication & cross-market analysis notebook
│
├── filtered_nbbo_data.part1.rar           # U.S. Equity (AAPL, AMZN) NBBO quote stream (Part 1 of 2)
├── filtered_nbbo_data.part2.rar           # U.S. Equity (AAPL, AMZN) NBBO quote stream (Part 2 of 2)
│
├── raw.part1.rar                          # Crypto perpetuals quote & trade datasets (Part 1 of 2)
└── raw.part2.rar                          # Crypto perpetuals quote & trade datasets (Part 2 of 2)
```
### 3. Project Folder Structure and input files


After running config cells inside notebook, following Directory Scheme will be ready to use.
To easily run codes afterwards, put extracted raw files in "raw" folder.

```text
.
├── price_impact_order_book_events.ipynb   # Master replication & cross-market analysis notebook
│
├── data/
│   ├── raw/                                # Compressed raw top-of-book crypto quote streams (.csv.gz)
│   │   ├── BTC/
│   │   ├── ETH/
│   │   ├── SOL/
│   │   ├── HYPE/
│   │   └── DOGE/
│   ├── raw_trades/                         # Compressed raw crypto trade streams (.csv.gz)
│   │   ├── BTC/
│   │   ├── ETH/
│   │   ├── SOL/
│   │   ├── HYPE/
│   │   └── DOGE/
│   ├── raw_stocks/                         # Filtered stock NBBO data
│   │   └── filtered_nbbo_data.csv          # Uncompressed stock quote file (~2.7M rows)
│   ├── processed/                          # 10s aggregated Parquet/CSV files for crypto assets
│   └── processed_stocks/                   # 10s aggregated CSV files for AAPL and AMZN
│
└── results/                                # Generated output tables, regression figures, and CSV summaries
    ├── BTC/
    ├── ETH/
    ├── SOL/
    ├── HYPE/
    ├── DOGE/
    ├── stocks/                             # Stock-specific regression tables and intraday plots
    └── market_comparison/                  # Cross-market synthesis plots and final summary CSVs
