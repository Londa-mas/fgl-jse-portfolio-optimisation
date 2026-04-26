# fgl-jse-portfolio-optimisation
Implementing a Graph-Aware Factor Graphical Lasso (FGL) framework for the Johannesburg Stock Exchange. This project optimises portfolio resilience by penalising idiosyncratic network hubs to mitigate systemic contagion.

# Graph-Aware Factor Graphical Lasso (FGL) for JSE Portfolio Optimisation

## 1. Project Overview
This repository implements a robust portfolio optimisation framework based on the **Factor Graphical Lasso** method introduced by Lee and Seregina (2023). While the original research focues on the S&P 500, this project extends the methodology to the **Johannesburg Stock Exchange (JSE)**.
The core innovation is a **'Graph-Aware' optimiser** that utilises idiosyncratic network topology to penalise systemic hubs, thereby enhancing capital preservation during periods of market stress.

---

## 2. Project Structure
The repository is organised to follow the logical progression of the research, from data acquisition to systemic stress testing.

### Core Files
* **`FGL_JSE_Analysis.ipynb`**: The primary Jupyter Notebook containing the full end-to-end pipeline. It is documented with functional objectives and theoretical links to the underlying FGL paper.
* **`requirements.txt`**: A list of all Python dependencies (e.g., `cvxpy`, `scikit-learn`, `networkx`) required to replicate the environment.
* **`README.md`**: This file, providing project context and structural guidance.

### Methodology Phases (Inside the Notebook)
1. **Benchmark Replication:** Preliminary testing on the S&P 100 to validate the FGL estimator.
2. **JSE Data Engine:** Autotmated retrieval and cleaning of JSE Top 40 constituents, handling South African market liquidity constraints.
3. **Factor Decomposition:** Using PCA to remove pervasive common factors and isolate idiosyncratic residuals.
4. **Graphical Lasso:** Estimating a sparse precision matrix to map conditional dependencies.
5. **Graph-Aware Optimisation:** Integrating Degree, Betweenness, and Eigenvector centrality as penalty terms in a Global Minimum Variance Portfolio (GMVP) setting.
6. **Performance Evaluation:** A rolling-window backtest net of 30bps transaction costs, including cumulative return comparisons and Sharpe Ratio analysis.

---

## 3. Key Findings
* **Network Stability:** Identified that JSE idiosyncratic risk is highly integrated, with major financial and resource tickers acting as primary systemic bridges.
* **Capital Preservation:** The Graph-Aware strategy maintained a significantly lower Max Drawdown (-1.10%) compared to an equal-weighted benchmark during the 2023-2024 window.
* **Systemic Robustness:** In a simulated -5 sigma 'Black Swan' event, the model preserved **21.62% more capital** than a naive allocation by successfully de-weighting central network hubs.
