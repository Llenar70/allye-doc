# LinearDML

LinearDML is a Double Machine Learning (DML) widget powered by econML that estimates heterogeneous treatment effects (CATE). It supports both binary and continuous treatments, exposes the key building blocks (nuisance models Y|X and T|X, cross-fitting, final effect model), and provides rich diagnostics including SHAP summaries for nuisance models, propensity score analyses, and uplift (Qini/AUUC) curves for binary treatments.

---

## Inputs

* **Data**
  * Type: `Orange.data.Table`
  * Description: The analysis dataset. It should contain a treatment variable (binary or continuous), an outcome variable (numeric), covariates (features), and optionally meta variables, sample weights, and group identifiers.

### Input Data Specifications

* **Treatment Variable**
  * Binary: Use a Discrete Variable. Select the control group value in the UI; it will be encoded as 0 (the other value is encoded as 1).
  * Continuous: Use a numeric (Continuous Variable).
* **Outcome Variable**
  * Numeric variable to evaluate the effect (e.g., sales, conversion rate). Internally treated as float.
* **Covariates**
  * Numeric and/or categorical features. Categorical covariates are one-hot encoded (column names like `feature=value`, using `drop_first=True`).
* **Meta Variables (optional)**
  * Extra identifiers or information to be kept in the data.
* **Sample Weights (optional)**
  * Numeric weights applied throughout the estimation.
* **Group Column (optional)**
  * Group IDs for GroupKFold cross-fitting and cluster-robust bootstrap.

### Example Input Table

| User ID (Meta) | Z1 (Cov) | Z2 (Cov) | cat1 (Cov) | T (Treatment) | Y (Outcome) |
| :------------- | :------- | :------- | :--------- | :------------ | :---------- |
| user001        | 0.21     | -1.20    | A          | Treatment     | 2.34        |
| user002        | -0.85    | 0.33     | B          | Control       | 0.90        |
| ...            | ...      | ...      | ...        | ...           | ...         |

---

## Outputs

* **Enhanced Data**
  * Type: `Orange.data.Table`
  * Description: The original data with one meta column added: `CATE` (per-sample conditional average treatment effect). This enables downstream analysis using the estimated individual effects.

---

## Feature Description

<p align="center">
  <img src="./imgs/lineardml_overview_placeholder.png" alt="lineardml_overview_placeholder" width="1000">
</p>


*(Figure: Placeholder for the overall interface of the LinearDML widget)*

### Control Area (Left Panel)

<p align="center">
  <img src="./imgs/lineardml_control_placeholder.png" alt="lineardml_control_placeholder" width="600">
</p>


* **Data Variables**
  * **Treatment Type**: Choose `Binary` or `Continuous`.
  * **Treatment Variable**: Select the treatment column. For binary treatment, also select the `Control group value` (encoded as 0; the other value is 1).
  * **Outcome Variable**: Select the numeric outcome column.
  * **Covariates**: Drag and drop variables used as features.
  * **Meta Variables**: Variables to keep but not use for modeling.
  * **Sample Weights (optional)**: Select a weight column if available.

* **Outcome Model (Y | X)**
  * Algorithm: `Ridge`, `Lasso`, `RandomForest`, `LightGBM` (falls back to RF if LightGBM is not available).
  * For linear models: Regularization strength slider (α = 0.001..1.000).
  * For tree models: `n_estimators`, `max_depth` controls.

* **Treatment Model (T | X)**
  * Binary treatment: `Logistic (Binary)`, `RandomForest`, `LightGBM`.
  * Continuous treatment: `Ridge (Continuous)`, `RandomForest`, `LightGBM`.
  * Regularization/Tree settings similar to the outcome model.

* **Cross-Fitting**
  * `Folds (K)`, `Repeats` (UI-only), `Group Column`, `Random Seed`.

* **Effect Model (Final Stage)**
  * Regularization: `Ridge (L2)`, `Lasso (L1)`, or `None` with an α slider for Ridge/Lasso.
  * `Auto-generate T × Z interactions` (PolynomialFeatures degree=2, interaction-only).
  * `Cluster-robust SE (by user_id)` and `Bootstrap (B)` for ATE confidence intervals.

* **Evaluation Settings**
  * `Test Set Size (%)`: Currently displayed for consistency; the present implementation computes diagnostics without a holdout split.
  * `PS Trimming [α, 1−α]`: Propensity trimming threshold used for overlap coverage and IPS-based diagnostics.

* **Execute**
  * Runs the analysis. The button is enabled once the required fields are set.

### Main Area (Right Panel)

<p align="center">
  <img src="./imgs/lineardml_main_results_placeholder1.png" alt="lineardml_main_results_placeholder1" width="1000">
</p>



* **Model Diagnostics**
  * **Metrics**:
    * `ATE (±95% CI)`: Average Treatment Effect and optional bootstrap CI.
    * `ATT / ATC`: Effects on treated/control (binary treatment only).
    * `Overlap Coverage`: Share of observations with trimmed propensity inside [α, 1−α].
    * `Nuisance Fit`: R² for Y|X and T|X OOF predictions.
    * `AUUC`: Area under uplift curve (binary treatment only).
  * **Propensity Score Diagnostics**: Histograms for treated vs control propensities (binary).
  * **Nuisance Models — Predicted vs Actual**: Sample plot of Y-hat and T-hat series.
  * **SHAP Summaries**: SHAP summary plots for the outcome and treatment nuisance models.
  * **CATE Distribution**: Histogram of predicted CATE.
  * **Effect Heterogeneity (Coefficients)**: Bar plot of the final effect model coefficients.
  * **Uplift Curve (Qini / AUUC)**: Uplift curve and random baseline for binary treatments.

---

## Usage Example

<p align="center">
  <img src="./imgs/lineardml_workflow_placeholder.png" alt="lineardml_workflow_placeholder" width="300">
</p>



*(Figure: File → LinearDML → Data Table)*

1. Load the dataset with the **File** widget.
2. Connect it to the `Data` input of the **LinearDML** widget.
3. In the control area, set:
   * `Treatment Type`, `Treatment Variable` (and `Control group value` when binary), and `Outcome Variable`.
   * Assign variables to `Covariates`, `Meta Variables`, and optionally select `Sample Weights` and `Group Column`.
   * Configure `Outcome/Treatment` models, `Cross-Fitting`, and the `Effect Model`.
   * Optionally adjust `PS Trimming` in `Evaluation Settings`.
   * Click `Execute`.
4. Inspect metrics and plots in the main area.
5. Connect the `Enhanced Data` output to a **Data Table** to view CATE as a meta column.

---

## Reporting Feature

The widget supports Orange’s standard reporting. The report contains:

* Model settings (treatment/outcome variables, covariates, effect regularization, cross-fitting, test size as displayed).
* Diagnostics (ATE, ATT/ATC, overlap coverage, AUUC when applicable).
* Embedded plots: Propensity diagnostics, nuisance predictions, SHAP (Y and T), CATE distribution, effect coefficients, and uplift curve.

---

## Detailed Logic

1. **Data Preparation**
   * Outcome is cast to float.
   * Binary treatment is encoded with the chosen control value as 0 (other value as 1). Continuous treatment is used as-is.
   * Categorical covariates are one-hot encoded (`drop_first=True`) with readable names (e.g., `cat1_A`).
   * Optional sample weights and group IDs are passed through.
2. **Estimation (econML DML)**
   * Builds Y|X and T|X nuisance models and the final effect model.
   * Uses K-fold cross-fitting (GroupKFold when a group column is provided).
   * Computes per-sample CATE and summary effects (ATE, ATT/ATC for binary treatment).
   * Optional bootstrap provides ATE confidence intervals; cluster-robust mode resamples groups.
3. **Diagnostics**
   * OOF predictions assess nuisance fits (R²).
   * For binary treatments, propensities and IPS-based uplift diagnostics are computed (Qini curve and AUUC; overlap coverage uses PS trimming α).
   * CATE is regressed on X to provide effect coefficients (with standardization).
4. **SHAP**
   * SHAP summaries are computed for both nuisance models (Y and T). The implementation tries generic, linear, and tree explainers for robustness.
5. **Enhanced Data**
   * Adds `CATE` as a meta column to the input table for downstream use.

---

## Performance Notes

* SHAP is computed on the full dataset and can be computationally heavy for large X. Consider simplifying models or reducing feature dimensionality.
* LightGBM is optional; if unavailable, the widget falls back to RandomForest.
* Cross-validation and permutation-style operations use parallelism where available (`n_jobs=-1`).


