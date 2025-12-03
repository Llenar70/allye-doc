---
id: allye-quickstart
title: Quickstart
slug: /allye/quickstart
description: A short path to set up Allye, run a first workflow, and confirm everything works.
sidebar_position: 1
---

# Quickstart

Follow these steps as a minimal end-to-end path. 
This quickstart uses the open data LendingClub Loan Data. Download the csv file from this link: [/Users/s-sekine/Documents/MyGitHub/allye-doc/web-docs/docs/get-started/data/loan.csv].

### 1) Visualization

<p>
  <img
    src="./img/quickstart1_Visualization.png"
    alt="Quick Start 1 - Visualization"
    style={{ maxHeight: '60vh', width: '50%', objectFit: 'contain' }}
  />
</p>

1. Drag and drop csv file onto the canvas.
2. From the left panel, create `Data Table` in the `Data` section and `Distribution` in the `Visualize` section, then connect them to the File widget.
3. Open the widgets to verify.



### 2) Data Manipulation

<p>
  <img
    src="./img/quickstart2_DataManipulation.png"
    alt="Quick Start 2 - Data Manipulation"
    style={{ maxHeight: '60vh', width: '50%', objectFit: 'contain' }}
  />
</p>


1. From the left panel, create `Group by (Allye)` in the `Transform` section.
2. In Group by, select `grade`; from Attributes, choose `int_rate` and `Mean`.
3. Create `Bar Plot (Allye)` from the `Visualize` section and connect it to the `Group by (Allye)` widget.
4. Select `grade` for the X-axis and `int_rate - Mean` for the Y-axis to see the average interest rate by grade.

### 3) Advanced Statistics

<p>
  <img
    src="./img/quickstart3_AdvancedStats.png"
    alt="Quick Start 3 - Advanced Statistics"
    style={{ maxHeight: '60vh', width: '50%', objectFit: 'contain' }}
  />
</p>

1. From the left panel, create `Regression Analysis` in the `Statistical Analysis` section and connect it to the File widget.
2. Choose `loan_amnt`, `term`, and `sub_grade` as Features; select `int_rate` as Target Variables; set Regularization to None, and choose the remaining options as shown in the image below.
3. Click the Apply button and review the Full Report that appears in the Main Area.

### 4) Agent

<p>
  <img
    src="./img/quickstart4_Agent.png"
    alt="Quick Start 4 - Agent"
    style={{ maxHeight: '60vh', width: '50%', objectFit: 'contain' }}
  />
</p>

1. Load the data onto the canvas.
2. Ask the Agent a question, for example: “I’ve loaded the loan data. Could you analyze and report on the determinants of interest rates?”
3. The AI generates and analyzes a workflow and reports the results. Be specific in your questions to get better answers.

The Agent can control only some widgets. See here for details on Agent capabilities and usage.
