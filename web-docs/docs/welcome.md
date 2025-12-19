---
sidebar_position: 1
---

# Allye Documentation

<p>
  <img
    src={require('./get-started/img/connected_visualization.png').default}
    alt="Connected visualization overview"
    style={{ maxHeight: '80vh', width: '100%', objectFit: 'contain' }}
  />
</p>



## Why Allye? - Hybrid Analysis Environment

Built upon the foundation of open-source Orange Data Mining, Allye is a no-code/code hybrid platform engineered for professional rigor. It combines the intuitive nature of visual programming with the flexibility of raw code, all while running locally, securely, and quickly (in-memory processing).

### Breaking the Compromise
Traditionally, analysts had to choose between the ease of GUI tools and the flexibility of coding. Allye eliminates this trade-off, offering a unified environment that serves both engineers and business analysts.

<table>
  <thead>
    <tr>
      <th></th>
      <th>Traditional Tools</th>
      <th>(Vibe) Coding</th>
      <th><b>Allye</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>Pros</b></td>
      <td>Speed, Easy</td>
      <td>Flexibility</td>
      <td
        rowSpan="2"
        style={{
          verticalAlign: 'middle',
          textAlign: 'center',
          fontWeight: 'bold',
          background: '#f5f6fa',
        }}
      >Hybrid</td>
    </tr>
    <tr>
      <td><b>Cons</b></td>
      <td>Limited Flexibility</td>
      <td>Limited Speed, Complex</td>
    </tr>
  </tbody>
</table>

**[> Explore Quickstart](./get-started/Quickstart.md)**


---

### Core Capabilities

#### 1. Hybrid Environment
*   **Flexibility meets Efficiency:** Start your analysis with code (like Jupyter) and instantly drop it into a visual workflow or vice-versa. You can seamlessly switch between writing logic and using GUI components.


#### 2. Interactive & Connected
*   **Inter-connected Views:** Allye Visualizations are all connected and interactive. Selecting data points in a scatter plot immediately highlights the same data across tables and histograms, revealing hidden patterns instantly.
*   **Structured Workflows:** Your trial-and-error process is preserved as a visual flow, making it easy to retrace steps or explain your logic to stakeholders.


#### 3. Automated Rigor: Quality Control Built-in
*   **Automatic Diagnostics:** Necessary validation metrics appear automatically—such as scree plots, biplot, loading barplot for PCA or balance checks, love plot for Propensity Score Matching, p-value, confidence interval, VIF and residual analysis for Regression Analysis—so you never miss critical sanity checks.
*   **Reliable Reporting:** The system highlights abnormal values (for example, when a p-value exceeds 0.05) to guide you toward correct analysis.


#### 4. Causal Inference: From "What" to "Why"
Business decisions require understanding cause and effect, not just correlations. Allye makes **Causal Inference** a core capability.
*   **Beyond A/B Testing:** We provide a comprehensive suite of quasi-experimental methods (PSM, DID, DML, Causal Forest) for scenarios where randomized control trials are impossible.
*   **Structured Discovery:** Explore causal structures and verify hypotheses visually without needing complex script implementation.


---

### Security & Performance
**Local. Secure. In-Memory.**

Allye is designed for environments where data privacy is paramount.
*   **Local Execution:** All processing happens on your local machine or on-premise server. Your data never leaves your environment.
*   **In-Memory Speed:** High-speed in-memory processing ensures that your train-of-thought is never interrupted by loading times.




**Powered by Open Source**: Allye is built upon the robust foundation of [Orange Data Mining](https://github.com/biolab/orange3) (and other various oss), enhanced with our proprietary AI technologies.
