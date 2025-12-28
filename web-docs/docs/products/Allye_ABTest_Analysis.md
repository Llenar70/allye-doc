---
title: Allye A/B Test Analysis
sidebar_position: 5
---

# Allye A/B Test Analysis

Allye A/B Test Analysis extends `Allye Base` with widgets designed for deeper experiment evaluation and causal inference.

It is built to cover four common situations:

1. A clean randomized experiment (control vs. treatment are well balanced).
2. A non-randomized setting where selection bias exists and adjustment is needed for a fair comparison.
3. A small sample size where you want to maximize detection power (e.g., CUPED via **Regression Analysis** in Allye Base).
4. Moving beyond an average effect (ATE) to estimate subgroup / individual-level effects (heterogeneous treatment effects).

To support these workflows, this package adds the widgets below on top of `Allye Base`.

## Key Features

### 1. Classical A/B Testing (Randomized Experiments)

| Widget | Description |
| :--- | :--- |
| **AB Test** | Supported in Allye Base. See [here](./Allye_Base.md) for details. |

### 2. Bias Adjustment (Non-randomized Comparisons)

| Widget | Description |
| :--- | :--- |
| **Propensity Score Matching** | Estimates propensity scores from covariates (logistic regression) and creates a balanced comparison set via matching (nearest-neighbor or caliper, configurable matching ratio, optional replacement). Provides balance diagnostics (e.g., standardized mean differences and distribution checks) and outputs matched data, propensity scores, and a balance report for downstream analysis. |
| **DID** | Performs Difference-in-Differences (DID) for time series / panel data. Supports fixed effects, optional covariate adjustment, parallel-trends diagnostics, and event study outputs around an intervention point, producing results and diagnostic tables for interpretation. |

### 3. Small Sample Size
| Widget | Description |
| :--- | :--- |
| **CUPED (=Regression Analysis)** | Supported in Allye Base. See [here](./Allye_Base.md) for details. You can also check [A/B Test Result Analysis Tutorial](../tutorial-advanced/AB_Test_Result_Analysis.md) for CUPED practical usage.|


### 3. Segmentation & Heterogeneous Treatment Effects

| Widget | Description |
| :--- | :--- |
| **k-Means** | Segments users/items into clusters for deeper analysis. Supports selecting a fixed number of clusters or searching a range and comparing silhouette scores; outputs annotated data with cluster labels and cluster centroids. |
| **Hierarchical Clustering** | Creates a dendrogram-based segmentation from a distance matrix and lets you cut/select clusters interactively; outputs selected/annotated data for follow-up analysis. |
| **LinearDML** | Double Machine Learning (econML) for estimating treatment effects with flexible nuisance models and cross-fitting. Produces CATE estimates with diagnostics and outputs an enhanced dataset for downstream segmentation and reporting. |
| **ForestDML (Causal Forest DML)** | A DML variant that uses a causal forest as the final-stage effect model to capture non-linear, heterogeneous effects. Outputs an enhanced dataset with CATE and diagnostic tables. |

### 4. Time Series Exploration

| Widget | Description |
| :--- | :--- |
| **Time Series Analysis** | Interactive time series visualization with aggregation (e.g., daily/weekly/monthly), trend and moving-average overlays, seasonality/decomposition tools, and optional forecasting (Prophet). Outputs selected data and forecast results for reporting. |

## Templates
To see practical examples of analysis using Allye Base, please refer to the [A/B Test Result Analysis Tutorial](../tutorial-advanced/AB_Test_Result_Analysis.md) and [Causal Inference Tutorial](../tutorial-advanced/Quasi_experimental_Analysis.md) sections.