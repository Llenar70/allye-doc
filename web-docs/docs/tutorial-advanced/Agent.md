---
sidebar_position: 6
---

# AI-driven analytics

_For your detective work, AI is an exceptionally capable partner. Whenever you get stuck in your analysis, let it assist you. Explore what AI can do._


### Analytics Agent

The Analytics Agent is an end-to-end AI feature that automatically runs analyses on the data sitting in your canvas, interprets the findings, reports the results, and even proposes the next actions.
Use the tips below to get the most out of the Agent:

1. Always load your data into the canvas before asking the AI a question. The AI cannot fetch data from local files, connect to databases, or run SQL on its own.
    - Use the `File`, `DB Connector` & `SQL Executor`, or `Google Spread Sheet` widgets to bring in data.
    - If your table data already appears in the workspace, the `Python Notebook` widget works as well.

2. As of 2025-12-01, the AI can operate limited widgets under `Visualization`, `Statistical Analysis`, and `Causal Analysis`.
    - It cannot generate widgets in other sections such as `Model`, though coverage will expand over time.
    - Availebls widgets: 
        - Visualize: `Bar/Line Plot`, `Scatter Plot`, `Distributions`
        - Data Manipulation: `Notebook`
        - Statistical Analysis: `Correlations`, `Regression Analysis`, `Binary Analysis`, `Poisson Regression`, `Time Series Analysis`
        - Causal Analysis: `AB Test`, `Propensity Score Matching`, `Causal Tree/Forest`, `Linear/Forest DML`, `DID`

3. The AI offers two modes: Copilot mode only builds and runs workflows, while Agent mode additionally outputs a PDF report, interprets the results, and proposes next steps. Choose the right one for your preference.
    - In Agent mode the AI owns the analysis end to end and reports its conclusions, but you should always review its output.
    - In Copilot mode you remain in control, and the AI simply assists with complex data transformations through python code generation or visualizations, statistical modeling.
    - Enable Agent mode by checking "Include reporting, interpretation..." in Agent Settings otherwise AI will be Copilot mode.

<p>
  <img
    src={require('./img/agent_setting.png').default}
    alt="Agent Setting"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>

4. In Agent mode, after every run the AI reviews whether the results satisfy the your request and continues working if anything is still missing.
    - Configure the maximum number of iterations via "Max Iteration" in Agent Settings.

5. To run two analyses in parallel, press `cmd + n` to open a new canvas. Each canvas hosts one process.

6. Most data transformations are written in Python and executed inside a Python Notebook widget.
    - When an exception occurs, the error log is automatically sent to the AI, which retries up to three times. 
    - If it still fails, the Agent stops and you need to fix the issue manually.


### Ask & AI Reporting

Every widget footer includes `AI Reporting` and `Ask` buttons to support your analysis.

* `AI Reporting`: Let the AI draft a report interpreting the results. If a Regression Analysis output feels hard to articulate, instruct the AI to write the report. Adjust the "Request to AI Allye" prompt whenever you need emphasis on a specific point.

<p>
  <img
    src={require('./img/ai_reporting.png').default}
    alt="AI Reporting"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>

* `Ask`: Use this to consult the AI about how the widget works or its detailed specifications. For example, if you are unsure how to use the `Formula` widget for light data wrangling, ask here.

<p>
  <img
    src={require('./img/ask_ai.png').default}
    alt="Ask AI"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>



### Notebook Assistant

Jupyter Notebook is fully integrated into Allye, so you can execute arbitrary Python code via the Python Notebook widget.
The Notebook Assistant generates code on demand in response to your requests.
