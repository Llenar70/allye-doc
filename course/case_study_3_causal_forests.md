# Case Study 3: From Comeback to Loyalty: Using Causal Forests to Discover the Right User Experience

## What You'll Learn in This Case Study

You're a product manager focused on increasing user retention and Lifetime Value (LTV). You've noticed a recurring pattern: users who were inactive for months sometimes return to your service—a "comeback." While this is great, you see that many of them quickly become inactive again, or "re-churn."

You might have a few ideas for re-engaging them, like offering a discount coupon or a tour of new features. However, these are just hypotheses. You're concerned that you might be overlooking other, more critical actions, and you lack the confidence to act on these hunches. Simply giving everyone the same offer feels inefficient and risks annoying users for whom the offer is irrelevant. Rather than testing random ideas, you want to first analyze your observational data to discover what organic behaviors *actually* lead to long-term retention. The key challenge is to understand **which user actions drive loyalty for which users** during that critical comeback moment.

In this case study, you'll learn how to use **Causal Forest**, a powerful machine learning method, to move beyond one-size-fits-all solutions and design a personalized retention strategy.

Specifically, you will practice the following steps:

-   Frame the business problem of reducing re-churn as a causal inference question.
-   Understand the intuition behind **Causal Forest** and how it estimates the *Individual Treatment Effect (ITE)*.
-   Design an analysis to discover if a spontaneous user action has a causal impact on retention, and for which user segments.
-   Use the discovered insights to create a data-driven, personalized CRM strategy for different user segments.
-   Implement the analysis in Allye and share the results with stakeholders using the AI Report feature.

<br>

## Beyond Average Effects: What is a Causal Forest?

In our previous case studies, we focused on the *Average Treatment Effect on Treated* (ATT)—the overall impact of an action across an treated population. But in the real world, a marketing campaign or a new feature rarely affects everyone in the same way. A discount that excites one user might be ignored by another who is more interested in new functionality.

Relying only on the average effect can be misleading. A strategy that is a huge success for one segment could be a complete failure for another. The average would just show a mediocre result, potentially leading you to abandon a great, targeted idea. This is the challenge of **heterogeneous effects**.

This is where **Causal Forest** comes in. It's a machine learning method that moves beyond averages to estimate the **Individual Treatment Effect (ITE)**—the causal effect of a treatment on each individual user. It helps you answer the crucial question: *"Who does my feature work for?"*

### How Does It Work Intuitively?

Imagine a single **Causal Tree**. It's like a regular decision tree, but instead of predicting *if* a user will churn, it predicts how much a treatment *changes their probability of churning*. It works by repeatedly splitting your users into smaller groups based on their characteristics (e.g., "Were they a paying user before?" or "Did they use feature X a lot?").

A **Causal Forest** is simply an "ensemble" of hundreds or thousands of these Causal Trees. By averaging the predictions of all the trees, it provides a much more robust and reliable estimate of the treatment effect for every single user. It allows you to find hidden patterns and identify the specific user segments that respond most positively (or negatively) to your intervention.

<p align="center">
  <img src="./imgs/course3_causal_forest_concept.png" alt="Causal Forest Concept" width="600">
</p>

<br>

## Designing the Analysis: What User Actions Drive Retention?

As a product manager, you need to understand the *'why'* behind user retention. Instead of testing pre-defined ideas like "Does a coupon work?", a more powerful approach is to first identify the organic user behaviors that causally lead to long-term engagement. Once you know *what actions* are critical, you can then design features and campaigns to encourage them.

You have two core hypotheses about these critical actions:

### 1. Clarifying Behavioral Hypotheses

-   **Hypothesis 1 (Re-Engagement Hypothesis)**: Users who spontaneously engage with a core feature (e.g., make a repeat purchase) shortly after coming back are re-establishing a habit and are more likely to be retained.
-   **Hypothesis 2 (Discovery Hypothesis)**: Users who explore new features after coming back are re-discovering the product's value and are more likely to be retained.

Your goal is to use Causal Forest on your existing **observational data** to test both hypotheses and see which spontaneous actions have a causal impact on retention, after accounting for user self-selection. This will require running two separate analyses.

### 2. Defining the Analysis from Observational Data

-   **Target**: Users who were inactive for 90+ days and returned in the last 14 days.
-   **Key Performance Indicator (KPI)**: Re-churn within 30 days (binary, 1 = churned again).
-   **"Treatment" Variables (User's Spontaneous Actions)**:
    -   Analysis 1 (Re-Engagement): `made_repeat_purchase_in_7d` (1 if the user made any purchase within 7 days of returning, 0 otherwise).
    -   Analysis 2 (Discovery): `used_new_feature_in_7d` (1 if the user tried a recently launched feature within 7 days of returning, 0 otherwise).
-   **Covariates (User Features)**: To account for the severe self-selection bias (e.g., enthusiastic users are more likely to re-purchase *and* try new things), we must use historical data as controls:
    -   `total_sessions_before_idle`: User's historical engagement.
    -   `last_paid_plan`: Was the user a paying customer?
    -   `favorite_category`: User's content preferences.
    -   `account_age_years`: User's tenure.
    -   `device_type`: What device do they use?
    -   `acquisition_channel`: How did we first acquire them?

<br>

## Analyzing with Allye

> [Download the sample data here](https://github.com/Llenar70/allye-doc/blob/main/course/sample_data/causal_forest_retention_data.csv)

### 1. Load Data

Drag & drop your `causal_forest_retention_data.csv` file to the canvas and view it with a `Data` widget. This dataset contains all the variables needed for both of your analyses.

<p align="center">
  <img src="./imgs/course3_load_data.png" alt="Load Data" width="700">
</p>

### 2. Set Up and Compare Two Causal Forest Models

To test both of your hypotheses, you will create two parallel `Causal Forest` widgets.

1.  **Model 1 (Re-Engagement Analysis)**:
    -   Connect a `Causal Forest` widget to your data.
    -   Set **Treatment Variable** to `made_repeat_purchase_in_7d`.
    -   Set **Outcome Variable** to `churn_within_30d`.
    -   Select all other relevant user features as **Covariates**.

2.  **Model 2 (Discovery Analysis)**:
    -   Create a second `Causal Forest` widget.
    -   Set **Treatment Variable** to `used_new_feature_in_7d`.
    -   Keep the **Outcome Variable** and **Covariates** the same as in Model 1.

<p align="center">
  <img src="./imgs/course3_two_forests.png" alt="Two Causal Forest Widgets" width="800">
</p>

### 3. Diagnose and Interpret Each Model

For each of the two models, investigate the diagnostic plots to understand the results:

-   **Distribution of ITEs**: Look at the overall distribution of the individual treatment effects for both "repeat purchase" and "new feature usage." This gives you a high-level view of how impactful each action is across the entire user base.
-   **Variable Importance**: Compare which user features are most important for predicting the effect in each model. Do the same user characteristics drive the effectiveness of both actions, or are they different? This is a crucial step for uncovering deeper insights.

<p align="center">
  <img src="./imgs/course3_ite_distribution_comparison.png" alt="ITE Distribution Comparison" width="800">
</p>

### 4. Create and Verify Segments for Each Insight

Based on the ITEs from *each* model, create and analyze user segments.

1.  Connect a `Select Rows` widget to the first Causal Forest (Re-Engagement). Filter for users where the predicted effect of a repeat purchase is strong (e.g., ITE for churn < -0.10).
2.  Connect another `Select Rows` to the second Causal Forest (Discovery). Filter for users where the predicted effect of using a new feature is strong.
3.  Use `Data Table` and `AB Test` widgets to inspect these segments. Do they overlap? Are the characteristics of the "high-potential for re-purchase" segment different from the "high-potential for new feature discovery" segment?

### 5. Share Your Findings with AI Report

Click the `AI Report` button on the canvas to get auto-generated insights that synthesize findings from your entire workflow. This is especially powerful when comparing the results of the two models.

<br>

## Making Decisions: From Behavioral Insights to Actionable Strategy

By running two separate Causal Forest analyses, you move beyond a single insight and can now develop a much more nuanced understanding of your comeback users. You're not just asking "what works?" but "what works for whom?"

-   **Key Insight: Different Actions Matter to Different Users**: Your analysis reveals two distinct user personas with different drivers of retention:

-   **Persona A: The "Habit-Driven Buyer"**:
    -   **Behavior**: For users who were previously on a **paid plan** and favored the **"Fashion" category**, the act of making a *repeat purchase* has a powerful causal effect, reducing their re-churn probability by up to 15 percentage points.
    -   **Interpretation**: For this group, retention is about re-establishing a routine. Their past investment (paid plan) and clear preference signal that they return looking for familiar value.
    -   **Action Driver**: The key driver for this group is `made_repeat_purchase_in_7d`.

-   **Persona B: The "Curious Explorer"**:
    -   **Behavior**: For users who were historically on a **free plan** and had high engagement (`total_sessions_before_idle`), the act of *using a new feature* has the strongest causal impact on their retention. For them, a repeat purchase shows little to no effect.
    -   **Interpretation**: This group is not driven by past purchasing habits. They are looking for new value and innovation in the product. Their retention is sparked by discovering new functionalities.
    -   **Action Driver**: The key driver for this group is `used_new_feature_in_7d`.

### Crafting a Data-Driven, Persona-Based Retention Strategy

These causal insights allow you to replace a one-size-fits-all approach with a sophisticated, persona-driven strategy:

1.  **Confirm the "Why" for Each Persona**: You have validated that different behaviors are critical for different user segments. You've moved beyond correlation to understand the causal drivers of retention for distinct groups.

2.  **Define Persona-Specific Goals**:
    -   **For Habit-Driven Buyers**: The goal is to **incentivize a repeat purchase** shortly after they return.
    -   **For Curious Explorers**: The goal is to **guide them toward discovering new features**.

3.  **Design Targeted Interventions (The "How")**: Now you can design precise A/B tests to confirm that your interventions successfully encourage these key behaviors.
    -   **Test for Persona A (Habit-Driven Buyers)**:
        -   **Treatment Group**: Offer a **time-sensitive discount coupon** for the "Fashion" category to incentivize that crucial repeat purchase.
        -   **Control Group**: Give this segment no offer.
        -   **Hypothesis**: The coupon will increase repeat purchases, which will causally decrease re-churn for this specific segment.
    -   **Test for Persona B (Curious Explorers)**:
        -   **Treatment Group**: Upon their return, trigger a **guided tour or tooltip highlighting a major new feature** relevant to their past activity.
        -   **Control Group**: Show them the standard welcome-back message.
        -   **Hypothesis**: The feature tour will increase new feature adoption, which will causally decrease re-churn for this segment.

By first using Causal Forest to identify *which behaviors matter to whom*, you can design smarter, more targeted, and more effective experiments. You're no longer guessing what might work; you're building a causal chain from persona to action to outcome.

<br>

## Real-World Hurdles and Beyond

-   **Unobserved Confounding**: Causal Forest can only account for the features you provide it. If a critical unobserved factor exists (e.g., the user's *reason* for coming back), the ITE estimates could be biased.
-   **Treatment Costs**: If your treatment has a cost (like a discount), you should factor this into your decision-making, focusing on segments where the increase in LTV outweighs the cost.
-   **Combining with Online Experiments**: The best practice is to use Causal Forest to *generate hypotheses* about segments, and then run new, targeted A/B tests on those segments to confirm the effect.

In our next case study, we will explore **Difference-in-Differences (DID)**, a powerful method for measuring the impact of large-scale changes, like a new store layout.

---
> [Next: Case Study 4: Evaluating Store Layout Changes with Difference-in-Differences](./case_study_4_difference_in_differences.md)
