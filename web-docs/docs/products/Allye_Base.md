---
title: Allye Base
slug: /products/allye-base
sidebar_position: 1
---

# Allye Base

Allye Base is the core foundation of the Allye platform, equipping you with essential tools for data acquisition, transformation, interactive & connected visualization, and multivariate statistical analysis.

## Key Features

### 1. Data Management
Connect to various data sources, from local files to cloud databases, and manage your datasets seamlessly.

| Widget | Description |
| :--- | :--- |
| **File** | Import data from diverse file formats including CSV, Excel (xlsx), TSV, and Tab-delimited files. |
| **DB Connector** | Connect directly to databases. Supports **BigQuery**, **Redshift**, **SQL Server**, **PostgreSQL**, and **MySQL**. |
| **SQL Executor** | Execute custom SQL queries directly against your connected databases. |
| **Google Sheet** | Import data directly from Google Spreadsheets (*requires authentication*). |
| **Allye Data Receiver / Transmitter** | Exchange data seamlessly between Allye and external Python programs. |
| **Save Data** | Export and save your processed data as CSV or Excel files. |
| **Data Table** | View and inspect the raw data in a spreadsheet-like format. |

### 2. Visualization
Explore your data with a wide range of interactive charts. Allye's visualization widgets are "connected," meaning selections in one chart automatically propagate to others.

| Category | Widgets & Description |
| :--- | :--- |
| **Basic Visualization** | **Bar Plot**, **Line Plot**, **Box Plot**, **Scatter Plot**, and **Distributions** (Histogram). |
| **Advanced Visualization** | • **3D Scatter Plot**: Visualize data in three-dimensional space.<br />• **Feature Statistics**: Inspect basic statistical properties of data features.<br />• **Visualize Connector**: Acts as a hub to synchronize selection states across multiple visualization widgets. See [Connected Visualization](../get-started/Connected_Visualization.md) for details. |

### 3. Data Transformation & Preprocessing
Clean, shape, and enrich your data before analysis using powerful ETL tools.

| Widget | Description |
| :--- | :--- |
| **Python Notebook** | Execute arbitrary Python code within an integrated Jupyter Notebook environment. |
| **Select Rows / Columns** | Filter data by selecting specific rows or columns based on conditions. |
| **Group By / Pivot Table** | Aggregate data, calculate summaries, and reshape datasets using pivot logic. |
| **Merge** | Join multiple tables using standard SQL-style joins: **Inner**, **Left**, and **Outer** joins. |
| **Formula** | Create new variables or modify existing ones using mathematical formulas. |
| **Preprocess** | Perform extensive data cleaning, including normalization, missing value imputation (removal/interpolation), and discretization/continuization. |
| **Edit Domain** | Rename columns, change data types, and modify variable attributes. |
| **Data Sampler** | Randomly draw a subset of data points for training or testing. |

### 4. Statistical Analysis
Generate comprehensive statistical reports and build models with detailed diagnostics.

| Analysis Type | Description & Outputs |
| :--- | :--- |
| **AB Test** | Compares groups across multiple target metrics, and reports lift, confidence intervals, and p-values. Automatically selects an appropriate test (T-test / Chi-square / Mann–Whitney U) based on the target variable type, with optional multiple-comparison correction (Bonferroni / Holm / Benjamini–Hochberg). |
| **Regression Analysis** | Interactive linear regression (with optional L1/L2 regularization) and a full statistical report (not just predictions).<br />• **Metrics**: R², Adjusted R², MSE, RMSE, AIC, BIC.<br />• **Model insight**: Coefficients, confidence intervals and p-values (available when unregularized), and optional VIF to check multicollinearity.<br />• **Diagnostics**: Predicted vs. Actual and residual plots (Residuals vs Fitted, Normal Q-Q, Scale-Location, Residuals vs Leverage). |
| **Binary Analysis** | Logistic regression for binary outcomes (with optional L1/L2 regularization), designed for both interpretability and evaluation.<br />• **Metrics**: Accuracy, Precision, Recall, F1, AUC, Log Loss.<br />• **Interpretation**: Coefficients, odds ratios, and (when unregularized) confidence intervals and p-values; optional VIF for multicollinearity checks.<br />• **Plots**: ROC curve and confusion matrix. |
| **PCA** | Principal Component Analysis for in-depth understanding and dimensionality reduction.<br />• **Selection**: Choose a fixed number of components or target explained variance; optional normalization.<br />• **Outputs**: Transformed data, component loadings, and explained-variance summaries.<br />• **Visualizations**: Scree plot, loadings table/plot, and biplot. |

## Templates
To see practical examples of analysis using Allye Base, please refer to the [EDA Tutorial](../tutorial-advanced/EDA.md) section.
