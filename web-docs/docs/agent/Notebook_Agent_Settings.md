---
sidebar_position: 3
---

# Notebook Agent Settings
<div className="doc-tag-row">
  <span className="doc-tag doc-tag--green">Allye Advanced Feature</span>
</div>


### Tips & Configuration

Use the tips below to get the most out of the Agent:

**1. Tool selection**

| Tool           | Description                                                                                                  |
|-------------------|-------------------------------------------------------------------------------------------------------------|
| Notebook edit     | Grants the Agent permission to edit the notebook. With this permission, the Agent can create cells and generate Python code or markdown text within the notebook. |
| Notebook execute  | Grants the Agent permission to execute the notebook. With this permission, the Agent can interpret execution results and perform debugging if errors occur.       |

<p align='center'>
  <img
    src={require('./img/notebook_agent_tool.png').default}
    alt="Notebook Agent Tools"
    style={{ width: '60%', objectFit: 'contain' }}
  />
</p>

**2. Chat model, Auto-complete model**

You can select which model to use. As of 2025/12/7, Gemini 3 Pro offers the best performance, but it is also the most expensive model.

<p align='center'>
  <img
    src={require('./img/notebook_agent_settings.png').default}
    alt="Notebook Agent Settings"
    style={{ width: '70%', objectFit: 'contain' }}
  />
</p>
