# Poisson Regression

Performs Poisson (GLM) regression for count outcomes and visually evaluates results.

## Input

1. **Data**: Input dataset (`Orange.data.Table`)
   - The target variable must be a nonnegative integer count. If negative or non-integer values are found, the widget raises an error.
   - Explanatory variables can be numeric or categorical. Categorical variables are automatically one-hot encoded within the widget.
   - Optional meta variables are carried through to the `Predictions` output (and can be used as Cluster Key or Exposure/Offset sources).

## Output

1. **Coefficients**: Coefficient table (`Orange.data.Table`).
   - Contains COEF, IRR (= exp(coef)), Std. Error, P-Value, VIF, and IMPORTANCE (|coef|) for each variable.
   - P-Values/Std. Errors are not shown for regularized fits.

2. **Predictions**: Prediction results (`Orange.data.Table`).
   - The input data with an extra meta column `predicted_<target>` that contains the predicted mean count for each row. Original meta dtypes are preserved.

3. **Model**: The trained GLM Poisson model object. It provides helper methods such as `get_coefficients()`, `get_p_values()`, `get_standard_errors()`, `get_feature_names()`, and `evaluate(...)`.

## Feature Description

The Poisson Regression widget lets you select variables, configure model/link, handle exposure/offset, and review diagnostics tailored for count data.

### Control Area (Left Panel)

1. **Data Variables**
   - **Features**: Explanatory variables (numeric or categorical). Categorical variables are one-hot encoded with `drop='first'`.
   - **Target (count)**: A required count variable (nonnegative integers only).
   - **Meta Variables**: Variables that are not used in model training but are preserved in outputs; these can also be used in Exposure/Offset or Cluster Key.

2. **Model Type**
   - Only **Poisson Regression (GLM)** is available here.

3. **Link Function**
   - `log (canonical)` [recommended], `identity`, `sqrt`.

4. **Exposure / Offset**
   - **Use Exposure**: Select a variable measured on the original scale (e.g., time-at-risk). The widget applies log automatically and adds it to the offset vector.
   - **Use Offset**: Select a variable already on the log-scale (e.g., `log(exposure)`). This is added as-is to the offset vector.
   - If both are provided, the two are summed to form the final log-offset.

5. **Regularization**
   - `None`, `L2 (Ridge)` with strength `α` (0.001–1.000). For regularized fits, P-Values/SE are not reported.

6. **Advanced Options**
   - **Fit intercept**: Include a constant term.
   - **Normalize features**: Standardize features before fitting.
   - **Robust variance (HC1)**: Use heteroskedasticity-consistent (HC1) standard errors.
   - **Cluster standard errors (by variable)**: Provide a Cluster Key (e.g., user_id, store_id). Standard errors are clustered at that key while coefficient point estimates remain unchanged. Recommended when repeated observations per entity exist. Avoid too few clusters.
   - **Calculate VIF (on sampled data)**: Compute VIFs on the design matrix (after encoding). Uses a random sample when rows exceed the specified sample size (default 10,000).
   - **Plot sample size (max points)**: Upper bound of points drawn for plots to keep the UI responsive.

7. **Apply**
   - When settings change, click Apply to refit and refresh the results.

### Main Area (Right Panel)

1. **Model Performance**
   - **Deviance**: Model deviance (lower is better).
   - **Pseudo-R² (McFadden)**: Computed as `1 - ll_model / ll_null` using `llnull`.
   - **Log-Likelihood**, **AIC**, **BIC**.
   - **Dispersion**: Pearson Chi-square / df. Values much larger than 1 indicate overdispersion. Consider robust SE or a Negative Binomial model if overdispersion is strong.

2. **Coefficients & IRR**
   - Table with COEF, IRR `exp(coef)`, Std. Error, P-Value, VIF, IMPORTANCE.
   - The accompanying plot shows coefficients (intercept excluded) with color by sign.

3. **Predicted vs Actual (Counts)**
   - Scatter plot comparing actual counts (x) and predicted counts (y). The closer points lie to the diagonal, the better the fit.

4. **Residual Diagnostics**
   - **Residuals vs Fitted** (Pearson residuals vs fitted means).
   - **Normal Q-Q** of standardized residuals with reference line.
   - **Scale-Location**: `sqrt(|standardized residuals|)` vs fitted.
   - **Residuals vs Leverage** with Cook’s distance contours when leverage is available (computed for up to 10k rows for memory efficiency).

5. **Calibration / Gain**
   - **Calibration by Predicted Quantile**: Compares mean actual vs mean predicted counts by quantile bins.
   - **Cumulative Gain**: Share of total actual counts captured as you move down the ranked population by predicted mean.

## Detailed Logic

### Data Preprocessing

1. **Domain Reconstruction**: Based on user selections of Features, Target, and Metas.
2. **Categorical Encoding**: One-hot encoding with `drop='first'`, `handle_unknown='ignore'`. The design matrix is dense for better plotting and evaluation performance.
3. **Validation**: The target is validated to be nonnegative integers; otherwise, an error is raised.
4. **Offset Construction**: Exposure (log applied) and/or Offset (already log-scale) are combined into a single offset vector.
5. **Missing Values**: Rows with non-finite values (features, target, offset) are dropped.
6. **Normalization**: If enabled, features are standardized before fitting (intercept is handled properly on the statsmodels side).

### Model Training

- Uses `statsmodels.api.GLM` with `family=Poisson(link=...)`. Intercept is added via `sm.add_constant` when requested.
- Regularization: when `L2` is chosen, `fit_regularized` is attempted; SE/p-values are not available under penalization.
- Robust/cluster standard errors are supported via `cov_type='HC1'` or `cov_type='cluster'` with the specified groups; on failure the model falls back to non-robust covariance and a warning is shown.
- VIFs are computed on the (possibly encoded) feature matrix, sampled if necessary.

### Evaluation

- Predictions are model means `E[y|x]` (counts per unit exposure if an offset is supplied).
- Residuals are Pearson residuals: `(y - mu) / sqrt(mu)`.
- Pseudo-R² (McFadden) uses `llnull` when available. Leverage is computed via a QR-based approach up to 10k rows.

## Usage Example

1. Load a dataset with a count target (e.g., number of events). Ensure the target is a nonnegative integer.
2. Select Features, set the Target (count), and optionally choose Meta variables.
3. Optionally set Exposure/Offset if the observation window varies across rows.
4. Choose the link function (log is recommended) and other options (regularization, robust/cluster SE).
5. Click Apply to fit; review performance metrics, coefficients/IRR, residual diagnostics, and calibration/gain.
6. Use `Coefficients` output to inspect coefficient statistics, and `Predictions` output to analyze predicted counts by row.

## Notes & Limitations

- Regularized fits (L2) do not provide p-values or standard errors.
- Cluster SE requires a meaningful grouping key with multiple observations per cluster; too few clusters can make inference unreliable.
- Strong overdispersion may call for quasi-Poisson or Negative Binomial models (not included in this widget).
