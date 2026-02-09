---
slug: causal-post-psm_deepdive
title: 'Rethinking PSM design in Production'
authors: [sho]
tags: [causal-inference, allye, data-science]
---

Welcome to the world of Causal Inference.

If you work with data in a production environment, you've likely heard of **Propensity Score Matching (PSM)**. You may have even implemented it using libraries like `causalinference` or `DoWhy`.

Writing the code isn't difficult. With the modern Python ecosystem, you can calculate propensity scores, perform matching, and estimate effects (ATE/ATT) with just a few lines of code.

But when asked, **"Can we really trust these results?"** can you confidently say "Yes"? If not, you lose credibility.
Or, could you answer immediately without breaking a cold sweat if a `Staff Data Scientist` fired these sharp questions at you?

*   "If you change the random seed, does the result flip from positive to negative?"
*   "Are these matches actually similar? Are you forcing pairs?"
*   "How does the conclusion change if you tighten the caliper slightly?"
*   "What are the characteristics of the data that was excluded (trimmed)?"

In this blog, we will thoroughly rethink the "classical" method of PSM from the perspective of modern production data science. We will also dig deep into the philosophy and specifications of why Allye's PSM Widget is designed not just as a calculation tool, but as a **"cockpit for protecting analysis quality."**

This should serve as a "map of the field" for beginners and an "implementation answer key" for experts.

<!-- truncate -->

---

## 1. The Data Scientist's Dilemma: Diagnostic Cost > Computational Cost

The difficulty of causal inference in practice lies not in the computational cost, but in the **"diagnostic cost."**

The moment you decide to use PSM because "we can't do an A/B test (RCT), so let's analyze observational data," a Data Scientist wanders into a "labyrinth of questions":

### The Checklist

1.  **Covariate Balance Check:**
    "Is the Love Plot clean? Did all variables fall below the threshold (SMD < 0.1)? If not, should I change the model or drop variables?"
2.  **Common Support Verification:**
    "Do the propensity score distributions overlap? Are samples with extreme scores (0.99 or 0.01) distorting the results?"
3.  **Robustness Guarantee:**
    "What about the influence of unobserved confounders?"
    "Does the result depend on the random seed? Did we get significance with seed=42 but lose it with seed=123?"
4.  **Sensitivity Analysis:**
    "If I change the caliper from 0.1 to 0.05, how does the number of matches decrease and how does the effect size change? How strict do I need to be to get closer to the 'truth'?"

If you try to verify these seriously in Python, even though the core analysis `model.fit()` is just one line, the **"boilerplate code for verification, visualization, preprocessing, exception handling, and result formatting"** balloons to hundreds of lines.

### The "Friday Night" Temptation

On a Friday night with a deadline looming, looking at massive warning logs (like ConvergenceWarning), a devil whispers:
"Just `fit()` it, and if no errors pop up, let's call it OK. Let's pretend... we didn't see the fine-tuning of the balance."

If you want to remain a top-tier data scientist, you shouldn't run from this "gritty rigor." However, writing hundreds of lines of verification code by hand every time is also unrealistic.
Allye's PSM Widget was designed to solve this dilemma and execute **"gritty rigor" with "overwhelming speed."**

---

## 2. Intuitive Understanding: Matching as "Finding Twins"

Before getting into the details of the specifications, let's intuitively review how PSM works. Without using formulas, it is **"searching for destined twins."**

### Why Simple Comparison Doesn't Work

Suppose you want to know the effect of a "discount coupon" on an EC site.
Comparing the average purchase amount of those who received the coupon (Treated) and those who didn't (Control) is meaningless.

Because coupons weren't distributed randomly.
They might have been given preferentially to "people who have shopped often in the past (loyal users)." In that case, they would have shopped a lot whether they had a coupon or not. This is **Selection Bias**.

### Approach to Counterfactuals

What we really want to know is the difference:
"If Mr. A, who received a coupon, **had not received a coupon (counterfactual)**, how much would he have spent?"

However, unless we have a time machine, the real Mr. A has already received the coupon, so we cannot observe "Mr. A in the world line where he didn't receive it."

This is where PSM comes in.
From the entire data, we find Mr. B who **"is exactly like Mr. A in age, gender, past purchase history, residence, etc., but happened not to receive a coupon,"** and pair them up.

This Mr. B is effectively "Mr. A's twin (proxy)."
If we compare this pair, the difference is likely due to the "effect of the coupon" rather than "personal qualities." Doing this for the entire data is PSM.

### Hidden Pitfalls

However, reality is not that simple.

*   **Forced Pairing:**
    What if there is no one like Mr. A in the control group? If you forcibly bring in "Mr. C who is somewhat similar (but actually not very similar)," the results will be distorted. Checking "Common Support" prevents this.
*   **Just Happened to be Similar:**
    What if you chose the most similar person, but that Mr. B's data happened to be a measurement error (noise) or an outlier? 1-to-1 matching is weak against such noise.

That is why **"the courage not to match if they are not similar (Caliper)"** and **"checking if we rely too much on specific partners (Robustness Check)"** are essential.

---

## 3. Deep Dive into Allye PSM Widget Specifications

From here, I will explain the specific specifications of Allye's PSM Widget and the philosophy behind them. I will unravel why so many setting items are necessary from a practical perspective.

### 3-1. Consistency of Preprocessing and Model Estimation

The quality of PSM is more than half determined by the stage before matching, that is, "Propensity Score Estimation."
Allye's Widget strictly manages this process internally, which tends to become a black box.

*   **Missing Value Handling (Listwise Deletion):**
    Automatically detects missing values (NaN) in the selected covariates and excludes them row by row. This maintains consistency between score estimation and matching.
*   **Categorical Variable Encoding:**
    Automatically performs One-hot encoding. Real-world data contains a lot of categorical variables like "Gender," "Prefecture," and "Membership Rank," but this saves the trouble of manually creating dummy variables.
*   **Model Estimation (Logistic Regression):**
    Logistic regression (L1/L2 regularization supported) is used to calculate propensity scores.

### 3-2. Matching Target: The "Direction" of ATT and ATC

Even if we say "treatment effect," there is actually a direction. Many beginners stumble here. In Allye, you can clearly switch the `Matching Target` as follows:

*   **ATT (Average Treatment Effect on the Treated):**
    *   **Definition:** Makes "people who actually received the treatment (Treated)" the main characters (anchors) and looks for "people who did not receive the treatment (Control)" who are similar to them.
    *   **Business Question:** "How much did people who used the coupon gain compared to if they hadn't used it?"
    *   **Frequency in Practice:** Highest. Usually used for verifying the effect of measures.

*   **ATC (Average Treatment Effect on the Controls):**
    *   **Definition:** Makes "people who did not receive the treatment (Control)" the main characters and looks for "people who received the treatment (Treated)" who are similar to them.
    *   **Business Question:** "If people who didn't use the coupon had used it, what would have happened?"
    *   **Significance in Practice:** Can be used for "discovering potential demand." For example, finding a segment that is currently outside the target of the measure but would show effects if targeted.

This is not just a difference in labels. It fundamentally changes the behavior of the algorithm: **"Which group to keep in full, and which group to sample from."**
If you analyze without being conscious of this, you will end up giving an answer that deviates from the business question (Estimand mismatch).

### 3-3. The Courage to "Give Up If Not Similar": Caliper

The default of many libraries (Nearest Neighbor) tries to find "the closest partner anyway."
However, matching a person with a propensity score of `0.9` and a person with `0.4` cannot be called "twins." They are just "strangers."

In Allye, you can intuitively set the **Caliper**.
By imposing a constraint such as "only accept partners within a score difference of `0.05` (set in standard deviation units, etc.)," the quality of matching (Balance) is guaranteed.

*   **Visualization of Trade-off:**
    *   Tighten Caliper (smaller value) -> Matching quality increases (Bias decreases), but partners are not found and sample size decreases (Variance increases).
    *   Loosen Caliper (larger value) -> Sample size can be secured, but dissimilar partners are mixed in, distorting the estimation result (Bias increases).
    
    Allye's strength is that you can check this trade-off in real-time while moving the slider. It supports the decision: "Should we prioritize quality even if the sample size is halved?"

### 3-4. Obsession with Robustness: Randomize from Top-N (Caliper)

This function is not a "convenience feature," but **a specification for Sensitivity Analysis**.
The aim is not to swallow the result of a single matching whole, but to verify **"whether the conclusion holds even if the way neighbors are taken changes slightly."**

Usually, within the Caliper, the "closest one person" is selected.
However, in practice, it is not uncommon for there to be multiple candidates who are similarly close.
If you proceed with one fixed person in this case, there is a risk that the estimation result will rely too much on a specific partner.

Allye has an option called **`Randomize from Top-N (Caliper)`**.

*   **Behavior:** Instead of fixing the closest one, it **"creates a list of Top-N candidates (e.g., top 5) in the neighborhood and randomly selects one (or ratio amount) from them."**

#### Implementation Image by Pseudo Code

```python
# Processing image for each anchor (one person in the treatment group)
for anchor in anchor_units:
    # 1. Find candidates within Caliper range
    candidates = find_candidates_within_caliper(anchor, caliper_value)
    
    if len(candidates) == 0:
        discard(anchor) # Exclude because no match partner
        continue

    # 2. Sort by distance
    sorted_candidates = sort_by_distance(candidates, anchor)
    
    # 3. Get Top-N (e.g., top 5)
    # These are all "sufficiently similar (within Caliper)" and "top class close" people
    top_n_pool = sorted_candidates[:Top_N]
    
    # 4. Randomly choose one from them (This is important!)
    match = random.choice(top_n_pool, seed=current_seed)
    register_match(anchor, match)
```

*   **Why is this necessary:**
    This allows you to **change the random seed (Seed) and run it many times to see the result distribution**.
    This is not just a stability check, but a test to see "if it is a problem setting that PSM can handle."
    
    *   **Case A:** When the Seed was changed, the effect (ATT) changed from `+1000 yen` to `-500 yen`.
        *   -> **Judgment:** The PSM approach to this question is a red flag.
            The conclusion is unstable against the fluctuation of matching partners, and it is highly likely that it is strongly influenced by unobserved confounding or specification dependence.
    *   **Case B:** Even if the Seed was changed 10 times, the effect always stayed between `+900 yen ~ +1100 yen`.
        *   -> **Judgment:** It can be said that it is at least robust to "fluctuation of neighbor selection."
            Then, you can take the order of evaluating the residual risk of unobserved confounding in a separate layer.

Implementing and managing this with hand-written code and running loops to see the distribution is quite bone-breaking, but with Allye, you can check it immediately with one checkbox and Seed change.

### 3-5. Matching Ratio and Replacement

*   **Matching Ratio (1:1, 1:2...):**
    How many "twins" to assign to one anchor.
    *   `1:1` has the smallest bias, but much data is discarded.
    *   Increasing to `1:3` etc. increases the sample size and decreases variance (increases power). However, the second and third persons are likely "less similar than the first person," increasing the risk of increased bias.
*   **With Replacement:**
    Whether to match a partner who has been matched once with other anchors.
    *   If `True`, matching is easy to establish even if the control group is small, but since specific data is used many times, dependence on that data increases.
    *   If `False` (without replacement), one control person is used only once. Data independence is high, but if the number of control groups is not sufficient, the number of matches decreases drastically.

---

## 4. The Hierarchy of Robustness and Unobserved Confounding

Here lies the most important conceptual foundation for understanding Allye's Widget specifications.
"Reliability of analysis" in practice must be considered in the following three hierarchies. Allye is designed with this hierarchy in mind.

### Level 1: Stochastic Robustness
The level of "Does the result change every time I calculate?"
PSM includes random elements (handling ties when distances are exactly the same, sampling, Top-N randomization, etc.).
If the result turns from positive to negative just by changing the Seed, it is a problem before analysis. To clear this, we perform a stress test using the aforementioned `Randomize from Top-N`.

### Level 2: Design Robustness
The level of "Does it depend on the arbitrariness of parameters?"
"The effect appeared because I set the Caliper to `0.05`, but it disappeared when I set it to `0.01`."
"There is an effect with 1:1 matching, but no effect with 1:2."
In such cases, the result is likely an "artifact of parameter settings."
It is necessary to confirm that the conclusion (direction of effect and trend of magnitude) does not change even if parameters are moved (being insensitive). Allye's UI is for performing this parameter sweep at high speed.

### Level 3: Unobserved Confounding
And what remains at the end is the influence of factors that do not exist in the data.
For example, when you want to see the "effect of a coupon," what if the data only has "gender/age" and does not include "personal saving orientation" or "influence of TVCM seen by chance"?
**This cannot be erased no matter how much you calculate.** PSM can only balance "observed variables (covariates)."

### "Purifying" Risk and Determining Business "Resolve"

So, why are we so obsessed with Level 1 and Level 2? Isn't it useless if Level 3 (unobserved confounding) cannot be erased?

No, absolutely not.
**By thoroughly eliminating "noise that can be excluded (Level 1, 2: observation bias and calculation instability)," the outline of the "pure business risk (Level 3)" that remains at the end becomes clear for the first time.**

Making decisions without even adjusting for "known biases" like age and gender, and with unstable calculation results, is just reckless and negligence of a Data Scientist.
However, the uncertainty that remains after clearing Level 1 and Level 2 to the limit with Allye is no longer a defect of analysis, but a **"strategic risk (bet)"** that executives and leaders should bear.

What Allye provides is not a "100% guarantee." It is to draw a **boundary line** saying "This is what we can say from the data. Beyond this (the unobserved world) is your challenge."
Clear the fog of uncertainty, face the remaining risk, and step on the gas. Strict and fast PSM is necessary precisely to determine that "resolve."

---

## 5. Reverse Lookup Guide: Reading Settings by Intent

In practice, parameters should be chosen not by "which one to raise or lower," but by **"which bias to suppress and what to sacrifice."** I organized the main settings based on intent.

| Setting Item | What it Controls | Trend when Raised/ON | What is Sacrificed (Trade-off) |
| :--- | :--- | :--- | :--- |
| **Matching Method** (Caliper) | Selection of candidates | Strongly suppresses outlier matches (Bias down) | Matches decrease, estimation variance may increase |
| **Matching Ratio** (1:1 -> 1:3) | Partners per anchor | Variance decrease due to sample increase (Stabilization) | Easier to include distant partners, Bias up |
| **Caliper Value** (0.2 -> 0.05) | Allowed PS difference | Stricter matching as it gets smaller | Significant decrease in sample size |
| **Randomize from Top-N** | Randomness in neighborhood | Robustness (stability) of results can be confirmed | Analyst needs evaluation design (Seed Loop etc.) |
| **With Replacement** | Reuse of partners | Match possible even with small control group | Excessive dependence on specific data, damage to independence |
| **Random Seed** | Reproducibility and Explorability | Complete reproduction with same seed, sensitivity check with change | Interpretation of seed dependence required |
| **Trimming** | Common Support | Reduction of Extrapolation risk | Reduction of Target Population |

---

## 6. Proposed Workflow: A Rebuttal to "I Can Just Code This"

You might think, "I can just write this in Python." True, it is technically possible. However, the following problems occur in the field.

1.  **Bloating of Boilerplate:** Preprocessing, matching, visualization, aggregation... if you write these properly, it will be on the scale of 1000 lines.
2.  **Breeding Ground for Bugs:** Complex code breeds bugs. Mistakes like "logic of replacement was wrong," "sort order was reversed," or "rows shifted in handling missing values" are extremely difficult to discover.
3.  **Personalization:** A situation where Person A's implementation and Person B's implementation behave slightly differently is an organizational nightmare.

### Recommended Allye Workflow

With Allye, the following "advanced analysis flow" becomes standard.

1.  **Step 1: Create Baseline (Rough Sketch)**
    First, grasp the overall picture with default settings (or looser Caliper). Check "Can we separate by propensity score in the first place?"
2.  **Step 2: Improve Quality (Balancing)**
    While looking at the Love Plot, tighten the Caliper and secure the balance of variables (SMD < 0.1). Trim areas where distributions do not overlap.
3.  **Step 3: Robustness Check (Stress Test - Stochastic)**
    Turn on `Randomize from Top-N` and run with several different Seeds. Confirm that the result (ATT/ATC) does not fluctuate significantly. If it fluctuates here, it is insufficient data volume or model failure.
4.  **Step 4: Sensitivity Check (Stress Test - Design)**
    Confirm that the conclusion does not change even if Caliper or Ratio is changed slightly. If "the effect remains even if tightened," the effect is real.
5.  **Step 5: Decision Making**
    Only after doing this far, report as "what can be said from the data." Note the possibility of unobserved confounding.

Being able to run this loop in a few minutes to tens of minutes is the greatest benefit of using the tool. You can verify at a speed different from the manual loop of correcting code, re-running, and outputting plots.

---

## Conclusion: A Cockpit for Protecting Analysis Quality

PSM is not a magic wand. If the data is bad (if important confounding variables are not taken), no matter how advanced the matching is, it is meaningless. The principle of Garbage In, Garbage Out does not change.

However, Data Scientists have an obligation to do **"the best within the observable range."**

*   Did you adjust the balance to the limit?
*   Did you eliminate unreasonable matching?
*   Did you confirm the robustness of the result?
*   Is the result reproducible?

Allye's PSM Widget is a tool to fill these checklists quickly and surely. By changing "time to write code" into "time to think and verify," your analysis will have more persuasive power and become a solid basis for supporting business decisions.

Remove the anxiety of Level 1 (Calculation) and Level 2 (Design), and face Level 3 (Business Risk) dignifiedly.
Please experience **"fast, strict, and empathetic"** causal inference using Allye.

---

## Appendix: Allye PSM Widget Detailed Specifications (Implementation Base)

From here, I will summarize the behavior of the Widget like a specification document so that it can be used for review and operation design.
It is the "concrete" on implementation against the thought part of the main text.

### A-1. Input and Output

**Input**

- `Data` (Orange Table)

**Main Output**

- `Matched Data`
- `Propensity Scores`
- `Balance Report`
- On-screen Model Diagnosis (AUC/Accuracy/LogLoss)
- On-screen Balance Diagnosis (Love Plot / SMD Table)
- On-screen Sample Size (Before/After)

### A-2. Calculation Pipeline (Internal Order)

1. Pre-sampling (if necessary)
2. Missing exclusion and variable formatting
3. Propensity score model estimation (Logistic Regression)
4. Overlap/Positivity diagnosis
5. Apply trimming if necessary
6. Execute matching (Nearest/Caliper, ATT/ATC, ratio, replacement)
7. Calculate balance index (SMD)
8. Calculate IPW if necessary
9. Generate output table and report data

This order is fixed, so it is reproducible with the same settings, same data, and same seed.

### A-3. Specifications of Major Parameters

| Parameter | Representative Value/Range | Meaning in Specification |
| :--- | :--- | :--- |
| Matching Method | `Nearest Neighbor` / `Caliper` | Rule for selecting match candidates |
| Matching Ratio | `1:1` / `1:2` / `1:3` | Number of partners per anchor `M` |
| Matching Target | `ATT` / `ATC` | Definition of anchor side (reference side) |
| Caliper Value | `0.01 - 1.0` | Allowed PS difference |
| With Replacement | `True/False` | Reusability of partners |
| Random Seed | `1 - 99999` | Reproducibility / Randomization control |
| Randomize from Top-N (Caliper) | `True/False` | Enable random selection within Caliper candidates |
| Top-N Candidates | `1 - 1000` | Size of random sampling population `N` |
| Sampling Mode | `Manual` / `All` | Presence of pre-sampling |
| Manual Max Rows | `1000 - 10,000,000` | Max rows for Manual |
| Trimming Mode | `None/Percentile/Overlap/Fixed` | Overlap guarantee strategy |
| Trim Percentile | `0.0 - 0.1` | Percentile threshold |
| Fixed PS Min/Max | `0.0 - 1.0` | Fixed trimming boundary |
| IPW Trim Percentile | `0.0 - 0.1` | Trimming for IPW stabilization |

### A-4. Strict Behavior of Randomize from Top-N (Caliper)

This option is **valid only during Caliper**.
The behavior is as follows:

1. caliper内候補を抽出 (Extract candidates within caliper)
2. PS距離で近い順にソート (Sort by PS distance)
3. 上位 `top-N` を候補プール化 (Pool top `top-N`)
4. その中から `M` 件をランダム選択（`M` は ratio 由来） (Randomly select `M` items from them)

Supplement:

- Works even with `replacement=False` (Respects reuse prohibition)
- When candidates are insufficient, it behaves like "take as many as possible"
- Inconsistencies like `top-N < M` are absorbed internally (Actual extraction population is treated to satisfy at least `M`)

### A-5. Design Memo per Matching Method

**Nearest Neighbor**

- Operation where `With Replacement` is practically forced as UI specification
- Easy to match in order of closeness, calculation is fast
- Outlier match suppression is weaker than Caliper

**Caliper**

- Can explicitly state "Do not match if not similar"
- Basically recommended for quality-oriented practice
- Easy to verify robustness with `Randomize from Top-N`

### A-6. Diagnosis/Warning Design

The Widget returns not only results but also diagnostic information.

- Sample size before/after Matching
- SMD Before/After
- Love Plot
- Overlap diagnosis (How much is outside common support)
- Warning at low match rate (e.g., consider replacement)
- Model convergence warning (if necessary)

Please be sure to check these as a set, not just the "effect size" alone when making decisions.

### A-7. Note on Reproducibility

`Random Seed` mainly affects:

- Pre-sampling
- Anchor processing order
- Top-N random selection

In other words, changing the seed is not just a "visual random number change" but a **stress test for matching design**.
Conversely, when reporting, it is mandatory to fix the seed and leave reproducible settings.

### A-8. Estimand and Display Value Organization

In practice, the following confusion must be avoided:

- `ATT/ATC` is the setting of "Matching Direction"
- `ATE` is the "Aggregation Definition of Effect Estimation"

In review materials, please always declare "which estimand was the main target this time" at the beginning.
Without this declaration, discussions will almost certainly not mesh.

### A-9. Scale of Implementation (Reference)

If you implement equivalent specifications by hand, realistically the following will be required:

- Preprocessing and type-safe conversion
- Matching algorithm branching
- Random number management
- Diagnostic visualization
- Exception handling
- Output formatting
- Testing

Including operational quality, the actual feeling is that it tends to be on the scale of `1,000 to 3,000 lines` (more including tests).
What determines quality here is not the amount of code itself, but the **analyst's design ability (assumptions, diagnosis, robustness design)**.

---

*You can try [Allye Base](https://www.ai-allye.com/) for free.*
