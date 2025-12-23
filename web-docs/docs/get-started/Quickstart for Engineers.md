---
id: allye-quickstart-for-engineers
title: Quickstart for Engineers
description: A short path to set up Allye, run a first workflow, and confirm everything works.
sidebar_position: 1
---

# Quickstart for Engineers


### 1.Installation

1. Install allye_data_connector.

```bash
pip install allye_data_connector
```

2. Launch Allye.

You can download Allye from [here](https://www.ai-allye.com/all-downloads).


### 2.Send a DataFrame to Allye Canvas:

```python
import pandas as pd
import seaborn as sns

import allye_data_connector
df = sns.load_dataset("iris")
allye_data_connector.send_dataframe(df, table_name='iris')
```
The `Allye Data Receiver` widget will be added to the canvas, and your data will automatically appear in Allye.

<p>
  <img
    src={require('./img/Receiver.png').default}
    alt="Python to Canvas"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>

*Note: Data transfer occurs locally, so please ensure that both Python and Allye are running on the same machine.*

### 3.Canvas Workflows

1. Create `Scatter Plot` from left dock `Visualize` section and connect from `Allye Data Receiver`


<p>
  <img
    src={require('./img/visualize.png').default}
    alt="Canvas Workflow Visualization"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>

2. Create `Regression Analysis` from left dock `Statistical Analysis` section and connect from `Allye Data Receiver`

<p>
  <img
    src={require('./img/modeling.png').default}
    alt="Canvas Workflow Modeling"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>


### 4. Read it back:

1. Use Allye's `Allye Data Transmitter` widget to send Allye data.

2. Receive it on the Python side, and execute subsequent processing. 

<p>
  <img
    src={require('./img/Transmitter.png').default}
    alt="Python to Canvas"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>
