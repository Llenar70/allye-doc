---
id: allye-pythonnotebook
title: Python Notebook
slug: /allye/python_notebook
description: A short path to set up Allye, run a first workflow, and confirm everything works.
sidebar_position: 5
---

# Python Notebook

Allye ships with a built-in Jupyter notebook, so you can create a Python Notebook from the Transform section.

<p>
  <img
    src={require('./img/python_notebook.png').default}
    alt="Python Notebook"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>



### Data input/output

**Input**: When a Python Notebook widget is created and connected to a Data Source, the code for sending and receiving data is generated automatically. The `bundle.get("datasets", [])` function fetches the data fed into the Python Notebook widget, and table data is automatically stored in the pandas DataFrame `df`.

**Output**: The `send_data_to_next_widget(df)` function automatically converts `df` to an Orange data table (the standard data format in Allye) and outputs it to the downstream widget.

Note:

```python

# Code to read data and output data
import pandas as pd
from widget_data_handler import (
    read_multiple_from_shared_memory,
    store_output_df,
    store_output_payload,
)

def send_data_to_next_widget(df):
    return store_output_df(
        df,
        signal_file_path=r"/xxx/notebooks_signals/signal_306461252512041512_complete.txt",
        file_path=r"/xxx/notebooks_signals/out_data_306461252512041512.pkl"
    )

# Read data bundle with fallback to manifest on disk if needed
bundle = read_multiple_from_shared_memory(
    manifest_name="nb_306461252512041512",
    manifest_size=348,
    manifest_path=r"/xxx/notebooks_signals/in_data_306461252512041512/manifest.json"
)

datasets = bundle.get("datasets", [])

df = pd.DataFrame()
if len(datasets) >= 1:
    df = datasets[0]["data"]
# Store the active input as default output
send_data_to_next_widget(df)
df.head()
```




### Notebook Assistant

If you need to perform advanced data processing that can’t be achieved with the standard Data `Transform` widget, use the `Python Notebook` widget instead. You don’t need to be an expert in coding—AI Assistant can help you generate the necessary Python code.

<p>
  <img
    src={require('./img/nb_assistant.png').default}
    alt="Notebook Assistant"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>


