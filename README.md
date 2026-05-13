# Graph-Aware Factor Graphical Lasso (FGL) for JSE Portfolio Optimisation

## 1. Project Overview
This repository provides a comprehensive implementation of the Topological-Robust Factor Graphical Lasso (TR-FGL) framework. Developed as a Master’s research project at the University of Johannesburg, this pipeline extends the standard Factor Graphical Lasso (FGL) model (Lee & Seregina, 2020/2023) by integrating graph-theoretic penalties into the portfolio optimization process.

The core objective is to mitigate idiosyncratic contagion on the Johannesburg Stock Exchange (JSE) by identifying systemic idiosyncratic hubs through sparse precision estimation and penalizing their weights using Eigenvector Centrality. This approach bridges the gap between high-dimensional statistical shrinkage and financial network theory.

---

## 2. Methodology & Theoretical Framework
The project is structured into several high-fidelity financial engineering phases:
* **Systematic Factor Neutralization:** Utilizing Principal Component Analysis (PCA) to decompose the $p=76$ JSE return universe. The model purges systematic risk by removing the top latent factors (targeting an 80% variance threshold), leaving only purified idiosyncratic residuals.
* **Sparse Precision Estimation:** Application of the Graphical Lasso to the idiosyncratic residuals. The model employs a cross-validated penalty parameter ($\alpha$) to estimate a sparse precision matrix ($\hat{\Omega}$), which maps the conditional dependencies between assets.
* **Network Centrality Horse Race:** A comparative analysis of Degree, Betweenness, and Eigenvector centrality to determine which topological metric best predicts realized idiosyncratic risk.
* **Graph-Aware Optimization:** A Global Minimum Variance Portfolio (GMVP) objective function augmented with a linear centrality penalty. The model objective is defined as:

$$ \min_{w} \left( w^{T} \hat{\Sigma}_{FGL} w + \lambda \sum_{j=1}^{p} c_{j} w_{j} \right) $$

Where $c_j$ is the centrality score. This objective function is subject to $\sum w_i = 1$, $w_i \geq 0$, and a strict 30bps transaction cost turnover constraint.

### Core Files
* **`Composite_JSE_FGL_Extension_Code.ipynb`**: The primary Jupyter Notebook containing the full end-to-end pipeline. It is documented with functional objectives and theoretical links to the underlying FGL paper.
* **`requirements.txt`**: A list of all Python dependencies (e.g., `cvxpy`, `scikit-learn`, `networkx`) required to replicate the environment.
* **`README.md`**: This file, a technical documentation and execution guide.

### Methodology Phases (Inside the Notebook)
1. **Data Integrity and Cleaning Protocol**
   * Asset Selection: Establishes a high-dimensional universe ($p=76$) of JSE-listed equities to ensure the dimensionality ratio ($p/n$) is sufficiently high (0.6129) to necessitate advanced shrinkage estimators.
   * Synchronization: Retrieves adjusted daily closing prices and synchronizes them across the temporal horizon (July 2023 to January 2024).
   * Stationarity: Computes logarithmic returns to ensure additive properties and time-series stationarity.
   * Liquidity and Completion Filtering: Excludes assets with more than 10% zero-return days or less than 95% temporal data density to prevent numerical singularity in the precision matrix.
2. **Tail-Risk and Methodological Diagnostics**
   * Leptokurtosis Analysis: Calculates excess kurtosis across the universe (averaging 3.51) to confirm fat-tails in JSE returns, justifying the use of the Graphical Lasso for structural stability.
3. **Factor Decomposition (Factor Purging)**
   * Systematic Risk Neutralization: Performs Principal Component Analysis (PCA) on returns to isolate common market factors.
   * Variance Thresholding: Purges systematic noise by identifying latent factors (typically around 26) that account for approximately 80% of total variance.
   * Idiosyncratic Residuals: Extracts the purified idiosyncratic component of returns, which is used for subsequent network construction.
4. **Sparse Precision Estimation (Graphical Lasso)**
   * Structural Optimization: Estimates the sparse idiosyncratic precision matrix ($\hat{\Omega}$) using a cross-validated penalty parameter ($\alpha$).
   * Conditional Dependencies: Captures direct partial correlations between assets, distinguishing true economic linkages from pervasive market noise.
5. **Topological Centrality Analysis**
   * Metric Generation: Calculates multiple graph-theoretic metrics, including Degree, Betweenness, and Eigenvector centrality, for every asset in the network.
   * Topological Horse Race: Conducts a predictive analysis to correlate these metrics with realized risk, validating which centrality measure best identifies systemic conduits in the JSE.
6. **Graph-Aware Portfolio Optimization**
   * Convex Optimization: Formulates a Global Minimum Variance Portfolio (GMVP) objective function using the CVXPY library.
   * Topological Penalty Extension: Integrates a linear centrality penalty ($\lambda \sum c_j w_j$) into the objective function to endogenously de-weight central network hubs.
   * Institutional Constraints: Applies long-only and 100% budget constraints to ensure the portfolio is practically investable.
7. **Performance Evaluation and Stress Testing**
   * Rolling-Window Backtest: Evaluates the model out-of-sample with 30bps transaction costs to calculate terminal wealth and Sharpe ratios.
   * Kupiec Likelihood Ratio (LR) Test: Tests the statistical calibration of the model's risk forecasts.
   * Recursive Safety Envelope: Visualizes daily realized returns against 95% VaR and CVaR boundaries to audit model reliability.
   * Systemic Stress Test: Simulates high-volatility 'Black Swan' events to measure the model's resilience and capital preservation capabilities relative to standard benchmarks.

---

## 3. Key Findings
* **Topological Risk Proxy (Metric Horse Race):** The diagnostic analysis confirms Eigenvector Centrality as the superior predictor of realized idiosyncratic risk on the JSE, with a correlation of 0.1487. This outperformance validates the model's architectural focus on "global" network influence over simple connectivity (Degree: 0.10) or brokerage positioning (Betweenness: -0.12).
* **Risk-Adjusted Alpha (Ablation Study):** In the terminal state evaluation, the Topological FGL extension increased the Sharpe Ratio to 7.499, a significant improvement over the 7.20 achieved by the Pure FGL replication baseline. This confirms that topological regularization provides measurable outperformance in the high-dimensional JSE environment.
* **Targeted Resilience (Hub Failure Stress Test):** In a targeted idiosyncratic shock involving 5 systemic hubs (OCE, MTN, HCI, ARL, and BTI), the TR-FGL model restricted the instantaneous loss to just -0.09%. This represents an incremental capital preservation of 0.54% relative to the standard Factor Graphical Lasso baseline (-0.63%) and a massive improvement over classical Minimum Variance portfolios (-1.51%).
* **Operational Stability (Turnover Analysis):** The TR-FGL strategy demonstrated superior efficiency with an average turnover of 0.1147. This is 19.2% lower than the Pure FGL replication (0.1420), proving that graph-aware constraints endogenously stabilize weights and reduce transaction cost friction in the South African market.
* **Tail-Risk Mitigation:** While traditional Markowitz portfolios suffered a maximum drawdown of -0.56% during the backtest window, the TR-FGL extension maintained structural stability, keeping drawdowns to a negligible level while achieving a higher terminal return than the equal-weighted baseline.

---

## Authors
* Maphoka Mahlomola
* Olonda Masuku
* Moketla Motlatso
