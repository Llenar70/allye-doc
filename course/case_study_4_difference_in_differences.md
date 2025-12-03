# Case Study 4: Measuring the Impact of a New Store Layout with Difference-in-Differences

## Introduction: A Director's Challenge

As a director overseeing brand strategy at an apparel company, I've seen our brand grow by focusing on unique designs and a quality customer experience. Recently, however, we've faced a critical challenge: **the aging of some of our physical stores.** My hypothesis was that our outdated store designs were not just hurting our brand image, but also actively discouraging customers and capping our growth potential.

To tackle this, we launched a radical experiment. We selected a handful of pilot stores and gave them a complete overhaul—new exteriors, new interiors, and a totally fresh layout.

With a background in statistics, I knew that the gold standard for measuring the impact of this change would be a Randomized Controlled Trial (RCT), or an A/B test. However, a full-scale rollout was prohibitively expensive. A limited, pilot implementation was our only feasible option.

In this case study, we'll use the data from this pilot to answer a critical question: **What was the pure, isolated impact of the store renewal on sales and footfall?** You, too, may have faced situations where you need to rigorously evaluate a policy's effectiveness under real-world budget and operational constraints.

In such quasi-experimental settings, a powerful analytical method called **Difference-in-Differences (DID)** allows us to derive credible insights.

## What You'll Learn in This Case Study

This case study will equip you with the skills to:

-   Understand the intuition behind Difference-in-Differences (DID) and the critical Parallel Trends Assumption (PTA) that underpins its validity.
-   Define treated and control groups, establish pre- and post-intervention periods, and prepare your data accordingly.
-   Run Allye's `DID` widget, test the Parallel Trends Assumption, and conduct an event study to visualize how the treatment effect evolves over time.
-   Interpret the results correctly and translate them into actionable business decisions, such as a full-scale rollout strategy.

<br>

## What is DID? An Intuitive Explanation

Difference‑in‑Differences compares changes over time between a treated group and a control group to isolate the effect of an intervention.

-   The core idea
    -   Step 1: Compute the within‑group change from “pre” to “post.”
    -   Step 2: Subtract the control group’s change from the treated group’s change. This removes any common time shocks and any time‑invariant differences between groups (e.g., stores in better locations).

-   A tiny numeric example
    -   Pre period: treated = 100, control = 90
    -   Post period: treated = 120, control = 95
    -   Within‑group changes: treated = +20, control = +5
    -   DID estimate: 20 − 5 = 15 (interpreted as the intervention’s average effect)

-   What DID removes and what it needs
    -   Removes: constant, time‑invariant differences across groups and shocks that hit both groups equally.
    -   Needs: the Parallel Trends Assumption (in the absence of treatment, treated and control would have evolved in parallel), minimal spillovers, and consistent measurement over time.

-   Extensions you will use in Allye
    -   Multiple periods and fixed effects (store and period)
    -   Event study to visualize pre‑trends and dynamic, post‑treatment effects
    -   Optional covariate adjustment for time‑varying controls

<br>

## Why DID (and not PSM or Causal Forest) in this setting?

When unobserved, time‑invariant confounders exist (e.g., persistent store‑location attractiveness), Propensity Score Matching (PSM) or Causal Forest may struggle because they rely on selection‑on‑observables. DID removes those time‑invariant confounders by differencing out pre/post changes between treated and control groups. DID is most suitable when the intervention begins at a known date and PTA is plausibly satisfied.

<br>

## Designing the Analysis

-   **Population**: A brick‑and‑mortar retail chain. A subset of stores implements the new layout (treated), while others remain on the old layout (control).
-   **KPIs**: Daily revenue (continuous), daily footfall (continuous).
-   **Treatment**: `treated_store` (=1 treated, 0 control)
-   **Time**: `date`
-   **Unit ID (optional)**: `store_id` if you have stable per‑store panels; omit when using repeated cross‑sectional or aggregated group‑time data.
-   **Intervention start**: `intervention_date` (YYYY‑MM‑DD)
-   **Optional covariates**: `store_size`, `avg_income_area`, `weekday`, `promo_intensity` (time‑varying covariates can be included via covariate adjustment)

-   **Data forms supported**: Both panel data (same stores over time) and repeated cross‑sectional or aggregated group‑time data (different stores sampled per period or statistics aggregated by group×time). For the latter, you will typically not have a stable `Unit ID` and will use a pooled DID without unit fixed effects.

<br>

## Analyzing with Allye

1. Load data
   - Import `store_panel.csv` (or your dataset) into a `Data` widget.
   - Required columns: time variable (e.g., `date`), treatment variable (e.g., `treated_store`), and outcome variable (e.g., `sales` or `footfall`). If you intend to use fixed effects, include a unit ID (e.g., `store_id`). If there is a common start date, you can set it directly in the widget.

2. Configure the DID widget
   - Connect a `DID` widget.
   - Treatment Variable: `treated_store`
   - Outcome Variable: `sales` (use `footfall` when analyzing visits)
   - Time Variable: `date`
   - Unit ID Variable: `store_id` (only if using fixed effects on panel data; omit for repeated cross‑section/aggregated data)
   - Control Group Value: `0`
   - Intervention Point: the layout rollout date (e.g., `2024-05-15`)
   - Options:
     - Fixed Effects: On (store fixed effects + period fixed effects) when you have stable unit IDs; otherwise leave Off (pooled DID)
     - Covariate Adjustment: Optional (e.g., `promo_intensity`, `weekday`)
     - Parallel Trends Test: On
   - Note: If you do not have stable unit IDs (repeated cross‑sectional or aggregated group‑time data), leave `Fixed Effects` Off and do not set a `Unit ID Variable`.
   
3. Event study (dynamic effects)
   - Event Study: Enabled
   - Reference Period: `-1` (the period right before the intervention)
   - Time Aggregation: `Weekly` (or another aggregation suited to your data)

<br>

## Diagnostics and How to Read Them

-   **Parallel trends test**: Check that pre‑treatment trends do not differ significantly between groups. Combine the visualization (pre‑period coefficients near zero) with the statistical test.
-   **Event study**: If pre‑period coefficients are around zero, PTA is supported. A persistent upward or downward shift after the intervention indicates the magnitude and duration of the effect.
-   **DID coefficient**: Read it as an average policy effect (close to an ATT under standard DID). With weekly aggregation, interpret it as the average weekly change.
-   **Covariate adjustment**: Compare adjusted vs. unadjusted models to assess robustness (sign and magnitude should be broadly stable).

<br>

## From Interpretation to Decisions (What, Why, So‑What)

-   **What**: Sign and magnitude of the DID estimate. Example: +7% on sales (p < 0.05).
-   **Why**: Hints about heterogeneity. Effects larger for big stores or more pronounced on weekdays, etc. (Use interaction terms or subgroup DID as supporting checks.)
-   **So‑What**: Rollout strategy. If effects are stable and PTA holds, proceed to wider rollout. If effects are conditional (time‑of‑day, store type), expand in stages or with guardrails.

<br>

## Real‑World Hurdles and Workarounds

-   **PTA violations (pre‑trend differences)**: If pre‑intervention coefficients systematically deviate from zero, consider endogenous rollout timing or concurrent policies. Remedies: better‑matched controls, adjusting the rollout window, difference‑in‑difference‑in‑differences, or synthetic control.
-   **Spillovers**: Nearby stores may be affected. Remedies: exclude buffer zones, use distance‑based controls.
-   **Concurrent policies**: Promotions or price changes at the same time. Remedies: covariate adjustment, careful logging to avoid double attribution, sensitivity analyses.
-   **Seasonality/day‑of‑week**: Use period fixed effects and appropriate aggregation.

<br>

## Sample Data

Use either of the following formats:

-   Panel data (with fixed effects): `store_id`, `date`, `treated_store`, `sales` (or `footfall`), `store_size`, `avg_income_area`, `weekday`, `promo_intensity`
-   Repeated cross‑sectional or aggregated group‑time data (no stable unit IDs): `date`, `treated_group_flag` (or similar), `sales` (or `footfall`), optional covariates/metadata

<br>

## Using Allye's Outputs

-   `Results Table`: DID estimates (coefficients, standard errors, p‑values, confidence intervals)
-   Diagnostics in UI: Parallel trends test results, model summaries, and event‑study plot are displayed in the widget’s main area.
-   Note: In the current version, additional output ports such as a diagnostics table or preprocessed “treated data” are not exported; use the UI and the `Results Table` output.

---
