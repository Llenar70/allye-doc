# How to Measure the True ROI of Your Digital Ad Campaign with Allye

Every marketer faces a fundamental question: is my advertising *actually* working? You see the numbers on your dashboard, but a nagging doubt remains. Are those conversions happening *because* of your ads, or would they have happened anyway?


<p align="center">
  <img src="./imgs/Digital_Ad_Campaign_with_Allye_marketer.png"
       alt="marketer_question" width="400">
</p>


Today, we'll show you how to move beyond correlation and find the true causal impact of your campaigns, turning that doubt into data-driven confidence.

## 1. The Challenge: Organic vs. Uplift

Let's paint a picture. You just spent **$50,000** on a major Facebook ad campaign. You open the campaign dashboard, and it proudly displays **1,000 conversions**. Success?

Maybe. But consider this:
*   How many of those 1,000 users were already loyal customers planning to buy something?
*   How many were already deep in your sales funnel and would have converted *anyway*?
*   Are you sure your ad wasn’t just an unnecessary intervention for conversions that would have happened organically anyway?

Answering this question is crucial for your next budget decision. You can't afford to reinvest in a campaign based on misleading data. You need to know the true, incremental lift your ads generated.

## 2. The Old Way: A Data Scientist's Task

Traditionally, answering this question was a complex, time-consuming task reserved for data scientists. It involved a significant amount of coding and statistical knowledge.

To give you a taste, here's what the process might look like in Python using popular libraries:

```python
# Import necessary libraries
import pandas as pd
import numpy as np
from sklearn.linear_model import LogisticRegression
from causalinference import CausalModel

# 1. Load and clean data
# Assume 'data.csv' has user demographics, 'saw_ad' (1 or 0), and 'converted' (1 or 0)
df = pd.read_csv('data.csv')
df.dropna(inplace=True)

# 2. Define variables
treatment = df['saw_ad'].values
outcome = df['converted'].values
covariates = df[['age', 'past_purchases', 'time_on_site']].values

# 3. Build a propensity score model (e.g., Logistic Regression)
# This model predicts the likelihood of a user seeing the ad based on their attributes
propensity_model = LogisticRegression()
propensity_model.fit(covariates, treatment)
propensity_scores = propensity_model.predict_proba(covariates)[:, 1]

# 4. Use a causal inference library to match users and calculate the effect
causal = CausalModel(Y=outcome, D=treatment, X=covariates)
causal.est_propensity_s(propensity_scores)
causal.est_via_matching(bias_adj=True)

# 5. Print the results
print(causal.estimates)

# ...and this is just a simplified version.
```

> This typically requires significant coding knowledge and time for setup and execution.

Seeing this, most marketers would simply say, "I'll just trust the dashboard." It’s complicated, slow, and inaccessible to most business users.

### Before We Show You the Easy Way: What is Propensity Score Matching?

The magic behind solving this problem is a technique called **Propensity Score Matching (PSM)**.

Think of it like this: To test a new fertilizer, you can't just compare a plant that got it with any random plant that didn't. You need to compare it to a *similar* plant—one with the same amount of sunlight, water, and soil type. You need an "apples to apples" comparison.


<p align="center">
  <img src="./imgs/psm_intuitive.png"
       alt="psm_intuitive" width="300">
</p>



PSM does this for people. It calculates a "propensity score" for every user—the probability they would have been exposed to your ad based on their characteristics (like age, location, browsing history). Then, it finds pairs of users—one who saw the ad and one who didn't—that have a nearly identical propensity score. By comparing the conversion rates of these matched pairs, you can isolate the true effect of the ad itself.

## 3. The Allye Way: 4 Simple Steps

Allye takes the complexity of Propensity Score Matching and turns it into a simple, no-code workflow. Here’s how you can find your true ROI in minutes.

### Step 1: Connect to Your Spreadsheet
Just input URL of your ad performance data. This should be a simple file containing user attributes (age, past purchases, etc.), a column indicating who saw the ad (the treatment group), and a column for the outcome (e.g., conversion).

<p align="center">
  <img src="./imgs/ad_cpn_input_data.png"
       alt="ad_cpn_input_data" width="300">
</p>

### Step 2: Use the Propensity Score Matching Node
In Allye's visual canvas, you build your analysis by connecting nodes. Simply select the "Propensity Score Matching" node from the analysis library and place it on your canvas. This tells Allye exactly what kind of causal analysis you want to perform.

`[Image Description: Allye's visual workflow builder. A box labeled "Source Data: campaign_data.csv" is on the left. An arrow connects from it to a newly placed node in the center labeled "Analysis: Propensity Score Matching (PSM)".]`

### Step 3: Configure & Run
Click on the PSM node to open its simple configuration panel. Define your treatment (the "saw_ad" column), your outcome (the "converted" column), and the user demographics you want to use for matching. Then, just click "Run". No code, no formulas.

`[Image Description: A close-up of the PSM node's settings panel. It features clear dropdown menus labeled: "Treatment Group (Who saw the ad?)", "Outcome (What happened?)", and "User Attributes for Matching (Covariates)". The user has selected the appropriate columns from their data. A large, blue "Run Analysis" button is at the bottom.]`

### Step 4: Understand the True Impact
Instantly, Allye performs the complex matching and calculations. It presents the result in a clear, easy-to-understand dashboard. Allye calculates the **Average Treatment Effect (ATE)**, showing you the actual number of *additional* conversions generated by your ad. Our AI Agent even provides a business-ready summary of the findings.

`[Image Description: The final results screen in Allye. A large heading reads "Campaign Causal Impact". Below it is a key metric in a large font: "Average Treatment Effect (ATE): +8.5%". A bar chart visually compares the "Ad Group Conversion Rate" (e.g., 20%) with the "Matched Control Group Conversion Rate" (e.g., 11.5%). To the right, an "AI Agent Insights" box contains a text summary: "The ad campaign directly caused an 8.5 percentage point lift in conversions..."]`

## 4. The Result: Confident, Actionable Insights

So, what does this all mean for your $50,000 campaign?

Instead of just a correlation (1,000 total conversions), you now have a **causal number**. The analysis reveals that your ad campaign generated **450 additional conversions** that would not have happened otherwise.

Now you can confidently report:
> "Our $50,000 campaign generated 450 incremental conversions at a cost of $111 per true conversion. This resulted in a **true ROI of 150%**."

This insight is a game-changer. It allows you to stop guessing and start making strategic decisions. You can now effectively optimize your future ad spend, reallocating budget from low-impact campaigns to high-impact ones, backed by causal evidence.

With Allye, measuring the true impact of your work is no longer a six-week data science project. It's a five-minute task that empowers you to prove your value and drive real business growth.