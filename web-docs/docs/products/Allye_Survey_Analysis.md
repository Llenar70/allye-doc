---
title: Allye Survey Analysis
sidebar_position: 10
---

# Allye Survey Analysis

Allye Survey Analysis extends `Allye Base` with widgets and templates designed for practical, business-focused survey analytics—going beyond simple complaint counts.

If you are new to this style of analysis, see the end-to-end tutorial: [Survey Analysis](../tutorial-advanced/Survey_Analysis.md).

This package is built around three common questions:

1. **How are dissatisfaction items related?**  
   Complaints often co-occur because they share a hidden root cause. You want to understand correlation structure and extract latent factors.
2. **Which issues matter most for outcomes like overall satisfaction, churn, or resignation?**  
   Not every complaint has the same business impact—prioritization requires driver/sensitivity analysis.
3. **Which pain points are shared vs. segment-specific?**  
   Different customer segments may have distinct frustrations. You want a clear, visual way to compare them.

To support these workflows, this package adds the widgets below on top of `Allye Base`.

## Key Features

### 1. Quick Aggregation & Connected Visualization

The basic workflow (filtering rows/columns, aggregation, and interactive charts) is supported in `Allye Base`. See [Allye Base](./Allye_Base.md) for details.

### 2. Identify Latent Factors (Root Causes)

| Widget | Description |
| :--- | :--- |
| **Factor Analysis** | Extracts latent factors from many numeric survey items (EFA) and helps you interpret them via a scree plot, suitability metrics (KMO / Bartlett), loadings barplot, and biplot. Supports factor selection (Kaiser / fixed / variance explained), rotation (Varimax / Promax), and extraction (PAF / ML). Outputs factor scores appended to the data plus factor loadings and scoring coefficients tables (requires numeric, non-missing features). |

### 3. Quantify Driver Impact (Sensitivity Analysis)

Sensitivity / driver analysis is supported in `Allye Base` via:

| Widget | Description |
| :--- | :--- |
| **Regression Analysis** | Models a continuous outcome (e.g., NPS, satisfaction score) and produces an interpretable report with coefficients, confidence intervals (when applicable), and diagnostics. |
| **Binary Analysis** | Models a binary outcome (e.g., churn, resigned vs. retained) with evaluation metrics and interpretable effect sizes (odds ratios). |

Tip: Use factor scores from **Factor Analysis** as compact, less-collinear inputs when modeling overall outcomes.

### 4. Map Common vs. Unique Issues Across Segments

| Widget | Description |
| :--- | :--- |
| **Correspondence Analysis** | Performs correspondence analysis on two categorical variables (e.g., `Segment` × `Issue`) and visualizes their association as a low-dimensional biplot with a scree plot and contribution-to-inertia views. Supports multiple normalization modes and outputs a coordinates table (components + `Variable`/`Value` metadata) for downstream visualization and reporting. |

### 5. Survey-Focused Data Transformation & Visualization

| Widget | Description |
| :--- | :--- |
| **Melt** | Converts wide survey tables into a long format (`id`, `item`, `value`). Useful for quickly comparing many question items with the same chart/table logic. Supports choosing a unique row identifier, ignoring non-numeric features, excluding zero values, and customizing generated column names. |
| **Transpose** | Transposes a data table (swap rows/columns) to switch the analysis perspective (e.g., treat questions as rows). Supports generating new feature names generically or from a chosen variable, with an option to remove redundant instances. |
| **Linear Projection** | Interactive 2D projection for multivariate numeric data with connected selection. Supports Circular placement, PCA placement, and (when a suitable discrete target is available) LDA placement. Includes feature selection and “Suggest Features” (VizRank) to help find informative axes for exploration. |


### 6. Clustering & Segmentation

| Widget | Description |
| :--- | :--- |
| **k-Means** | Segments users/items into clusters for deeper analysis. Supports selecting a fixed number of clusters or searching a range and comparing silhouette scores; outputs annotated data with cluster labels and cluster centroids. |
| **Hierarchical Clustering** | Creates a dendrogram-based segmentation from a distance matrix and lets you cut/select clusters interactively; outputs selected/annotated data for follow-up analysis. |

## Templates
To see a practical end-to-end workflow, refer to the [Survey Analysis](../tutorial-advanced/Survey_Analysis.md) tutorial.
