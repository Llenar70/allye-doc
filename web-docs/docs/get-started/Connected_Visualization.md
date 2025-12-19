---
id: allye-connected-visualization
title: Connected Visualization
description: A short path to set up Allye, run a first workflow, and confirm everything works.
sidebar_position: 5
---


# Connected Visualization

<p>
  <img
    src={require('./img/connected_visualization.png').default}
    alt="Connected visualization overview"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>

Connected visualization is an interactive way to explore data where multiple charts, tables, and widgets stay in sync with each other. Instead of analyzing each visualization in isolation, you select data points in one view and immediately see the same selection reflected everywhere else.

In the example above, a scatter plot, bar chart, histogram, and data table are all connected:

- When you select outliers in the scatter plot, those records are
  instantly highlighted in every other visualization widget.
- This synchronized selection makes high‑dimensional data easy to
  understand at a glance.


<p>
  <img
    src={require('./img/iris_interactive.png').default}
    alt="Iris Interactive overview"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>


By linking selection across all visual widgets, connected visualization supports faster insight, more intuitive exploration, and better decision‑making when working with complex, high‑dimensional datasets.
