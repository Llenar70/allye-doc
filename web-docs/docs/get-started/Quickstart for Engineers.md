---
id: allye-quickstart-for-engineers
title: Quickstart for Engineers
description: A short path to set up Allye, run a first workflow, and confirm everything works.
sidebar_position: 1
---

# Quickstart for Engineers


### 1.Installation


```bash
pip install allye_data_connector
```

### 2.Send a DataFrame to Allye Canvas:

```python
import pandas as pd
import seaborn as sns

import allye_data_connector
df = sns.load_dataset("iris")
allye_data_connector.send_dataframe(df, table_name='iris')
```


### 3. Allye Canvas:
1. Use `Allye Data Receiver` widget to receive the dataframe data.
2. Create `Scatter Plot` from left dock and connect from `Allye Data Receiver`

<p>
  <img
    src={require('./img/Receiver.png').default}
    alt="Python to Canvas"
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
