---
id: allye-quickstart
title: Quickstart
description: A short path to set up Allye, run a first workflow, and confirm everything works.
sidebar_position: 1
---

# Quickstart


This quickstart will walk you through a project using Allye's core features. By the end, you'll be familiar with simple visualization, data manipulation, modeling and Agent.

In this project we will use open data LendingClub Loan Data. You can download from this [link](https://raw.githubusercontent.com/Llenar70/allye-doc/main/web-docs/docs/get-started/data/loan.csv).



### 1.Visualization

<p>
  <img
    src={require('./img/quickstart1_Visualization.png').default}
    alt="Quick Start 1 - Visualization"
    style={{ maxHeight: '60vh', width: '80%', objectFit: 'contain' }}
  />
</p>

1. Drag and drop csv file onto the canvas. You can download from this [link](https://raw.githubusercontent.com/Llenar70/allye-doc/main/web-docs/docs/get-started/data/loan.csv)
2. From the left panel, create `Data Table` in the `Data` section and `Distribution` in the `Visualize` section. 
3. Connect edges to those widgets from `File` widget.
4. Open the widgets and you should see Raw data as well as histogram.


### 2.Data Manipulation

<p>
  <img
    src={require('./img/quickstart2_DataManipulation.png').default}
    alt="Quick Start 2 - Data Manipulation"
    style={{ maxHeight: '60vh', width: '80%', objectFit: 'contain' }}
  />
</p>


1. From the left panel, create `Group by (Allye)` in the `Transform` section.
2. In Group by, select `grade`; from Attributes, choose `int_rate` and `Mean`.
3. Create `Bar Plot (Allye)` from the `Visualize` section and connect it to the `Group by (Allye)` widget.
4. Select `grade` for the X-axis and `int_rate - Mean` for the Y-axis to see the average interest rate by grade.

### 3.Advanced Statistics

<p>
  <img
    src={require('./img/quickstart3_AdvancedStats.png').default}
    alt="Quick Start 3 - Advanced Statistics"
    style={{ maxHeight: '60vh', width: '80%', objectFit: 'contain' }}
  />
</p>

1. From the left panel, create `Regression Analysis` in the `Statistical Analysis` section and connect it to the File widget.
2. Choose `loan_amnt`, `term`, and `sub_grade` as Features; select `int_rate` as Target Variables; set Regularization to None, and choose the remaining options as shown in the image below.
3. Click the Apply button and review the Full Report that appears in the Main Area.
