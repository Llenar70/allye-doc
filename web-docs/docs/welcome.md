---
sidebar_position: 1
---

# Allye Documentation

Allye is an AI-powered analysis tool that understands your data and helps you analyze data faster and deeper. 

<p>
  <img
    src={require('./get-started/img/connected_visualization.png').default}
    alt="Connected visualization overview"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>



## Why Allye? - Hybrid Analysis Environment

Built upon the foundation of open-source Orange Data Mining, Allye is a **no-code/low-code hybrid platform engineered for professional rigor.** It combines the intuitive nature of visual programming with the flexibility of raw code, all while running **locally, securely, and very quickly** (in-memory processing).

### Breaking the Compromise
Traditionally, analysts had to choose between the ease of GUI tools and the flexibility of coding. Allye eliminates this trade-off, offering a unified environment that serves both engineers and business analysts.

| Feature | Traditional GUI Tools | Coding (Notebooks) | **Allye** |
| :--- | :--- | :--- | :--- |
| **Approach** | Tool-driven (Black Box & Limited Flexibility) | Code-driven (Limited Speed) | **Hybrid (Flexible & Quick)** |
| **Python** | Limited / Auxiliary | Primary | **Seamless Integration** |
| **Depth** | General Stats / ML | Depends on skill | **Automated Rigor** |
| **AI Role** | Chat / Explanation | Code Copilot | **Autonomous Generation & Execution** |

**[> Explore Quickstart](./get-started/Quickstart.md)**


---

### Core Capabilities

#### 1. Hybrid Environment: Code & GUI in Harmony
Unlike tools where Python is an afterthought, Allye treats Python as a **first-class citizen**.
*   **Flexibility meets Efficiency:** Start your analysis with code (like Jupyter) and instantly drop it into a visual workflow or vice-versa. You can seamlessly switch between writing logic and using GUI components.
*   **Actionable AI Agents:** AI generates, debugs, and executes Python code / No code workflows directly within your analysis pipeline.

**[> Explore Unified Environment](./get-started/Unified_Environment.md)**


#### 2. Automated Rigor: Quality Control Built-in
Allye ensures statistical rigor is never an afterthought.
*   **Automatic Diagnostics:** Necessary validation metrics appear automatically—such as scree plots, biplot, loading barplot for PCA or balance checks, love plot for Propensity Score Matching, p-value, confidence interval, VIF and residual analysis for Regression Analysis—so you never miss critical sanity checks.
*   **Reliable Reporting:** The system highlights abnormal values (for example, when a p-value exceeds 0.05) to guide you toward correct analysis.

**[> Explore EDA Tutorial](./tutorial-advanced/EDA.md)**


#### 3. Causal Inference: From "What" to "Why"
Business decisions require understanding cause and effect, not just correlations. Allye makes **Causal Inference** a core capability.
*   **Beyond A/B Testing:** We provide a comprehensive suite of quasi-experimental methods (PSM, DID, DML, Causal Forest) for scenarios where randomized control trials are impossible.
*   **Structured Discovery:** Explore causal structures and verify hypotheses visually without needing complex script implementation.

**[> Explore EDA Tutorial](./tutorial-advanced/AB_Test_Result_Analysis.md)**


#### 4. Interactivity: Connected Exploration
Static charts end the conversation; Allye’s visualizations start one.
*   **Inter-connected Views:** Selecting data points in a scatter plot immediately highlights the same data across tables and histograms, revealing hidden patterns instantly.
*   **Structured Workflows:** Your trial-and-error process is preserved as a visual flow, making it easy to retrace steps or explain your logic to stakeholders.

**[> Explore Connected Visualization](./get-started/Connected_Visualization.md)**

---

### Security & Performance
**Local. Secure. In-Memory.**

Allye is designed for environments where data privacy is paramount.
*   **Local Execution:** All processing happens on your local machine or on-premise server. Your data never leaves your environment.
*   **In-Memory Speed:** High-speed in-memory processing ensures that your train-of-thought is never interrupted by loading times.




**Powered by Open Source**: Allye is built upon the robust foundation of [Orange Data Mining](https://github.com/biolab/orange3) (and other various oss), enhanced with our proprietary AI technologies.
