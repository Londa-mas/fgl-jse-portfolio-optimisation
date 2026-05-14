# **Topologically Regularised Factor Graphical Lasso (TR-FGL)**

## **Replication and Extension of Lee & Seregina (2023)**

**Portfolio Optimisation & Risk Allocation | Johannesburg Stock Exchange (JSE)**

## **1. Theoretical Framework**

This project is centered on the replication and extension of the Factor Graphical Lasso (FGL) framework introduced by Lee & Seregina (2023) in their paper *'Optimal Portfolio Using Factor Graphical Lasso.'* The research addresses the fundamental failure of the sample covariance matrix in high-dimensional settings ($p \approx n$), where estimation error leads to extreme, unstable portfolio weights. In the context of the Johannesburg Stock Exchange (JSE), where sector concentration and systemic factor dominance are high, this model provides a mathematically rigorous alternative to traditional Mean-Variance optimization.

### **A. Foundational Replication: The FGL Framework**

Following Lee & Seregina (2023), we move away from the assumption that the precision matrix ($\Omega$) is naturally sparse. Because financial returns are driven by common market factors, we adopt a 'Low-Rank + Sparse' decomposition:

1. **Low-Rank Component (Systematic Risk):** We utilize an Approximate Factor Model via Principal Component Analysis (PCA) to isolate systematic JSE factors. By 'purging' these factors, we account for the pervasive market noise that prevents standard Graphical Lasso from converging.
2. **Sparse Component (Idiosyncratic Risk):** Once the factor structure is removed, the remaining idiosyncratic residuals are modeled using Graphical Lasso (GLasso). This estimates a sparse precision matrix ($\Omega$), which represents the conditional dependency structure of the assets.
  * **Financial Merit:** This ensures that the portfolio remains robust to heavy-tailed (elliptical) distributions, a common feature of JSE equity returns.

### **B. Proposed Extension: Centrality-Regularized GMV**

While the original FGL framework effectively identifies idiosyncratic risk, it treats all idiosyncratic connections with a uniform entry-wise penalty. Our extension, the Topologically Regularised FGL (TR-FGL), introduces structural risk regularization into the optimization.

We hypothesize that certain assets act as 'idiosyncratic hubs' - structural anchors in the market that facilitate volatility contagion. To mitigate this, we augment the Global Minimum Variance (GMV) objective with a linear penalty based on Eigenvector Centrality ($c_i$):

$$\min_{w} \left( w^T \hat{\Sigma}_{FGL} w + \lambda \sum_{i=1}^{p} c_i w_i \right) \$$

Subject to: $$\sum_{i=1}^{p} w_i = 1, \quad w_i \geq 0$$
* $\hat{\Sigma}_{FGL}$: The FGL covariance estimator reconstructed from purged residuals.
* $c_i$: The centrality score of asset $i$, identifying its structural importance in the dependency network.
* $\lambda$: The regularization parameter controlling the aversion to structural risk.

### **C. Core Research Objectives**
* Replication Integrity: Verify that the FGL estimator outperforms the unpenalised Global Minimum Variance (GMV) and naive 1/N benchmarks within the JSE universe.
* Structural Diversification: Demonstrate that penalizing 'hubs' reduces realized portfolio risk during idiosyncratic contagion events.
* Estimation Stability: Prove that the TR-FGL approach maintains superior out-of-sample stability compared to standard sample-based models.

---

## **2. Replication Protocol: Foundational FGL & GMV**

To ensure the validity of the extension, this repository includes a rigorous replication of the core modules from Lee & Seregina (2023). The following components serve as the mechanical baseline for the project:

### **A. Factor Decomposition & Precision Mapping**

The primary requirement for FGL is the extraction of the full precision matrix ($\Omega$).

* **Mechanism:** The script utilizes Principal Component Analysis (PCA) to estimate the factor structure. Following the paper’s methodology, we determine the number of factors ($k$) using an explained variance threshold (configured to $>80\%$).
* **Verification:** The precision matrix of the idiosyncratic residuals is generated using `GraphicalLassoCV`. This ensures that the L1-penalty ($\alpha$) is selected via cross-validation, satisfying the paper’s requirement for a sparse, well-conditioned estimator in high dimensions.
* **Code Location:** `Section B: Replication of the Factor Graphical Lasso (FGL) Framework`.

### **B. The Benchmark: Unpenalised Global Minimum Variance (GMV)**

As a control group, the script implements the unpenalised GMV portfolio.

* **Mechanism:** This estimator relies on the standard sample covariance matrix. In high-dimensional settings (like our 76-asset JSE universe), this matrix is typically ill-conditioned.
* **Purpose:** By including this script, we demonstrate the error maximization and volatility inherent in classical models, providing a direct performance contrast to the FGL and TR-FGL models.

### **C. Performance Verification (Figure Replication)**

The repository is configured to produce a comparative performance figure that replicates the empirical evidence format used in the original paper.

* **Replication Logic:** The 'Horse Race' module compares:
1. **Naive 1/N:** The equal-weighted benchmark.
2. **Unpenalised GMV:** The sample-based risk-minimization benchmark.
3. **Standard FGL:** The replicated model from Lee & Seregina (2023).


* **Code Location:** Run the cell labeled `--- COMPARATIVE VISUALIZATION ---` to generate the longitudinal cumulative returns and Sharpe Ratio comparisons.

---

## **3. Technical Execution & Mechanics**

### **A. Environment & Dependency Management**

This project uses a specialized stack for convex optimization and network analysis. To run the replication, install the following:

```bash
pip install -r requirements.txt

```

*Key Dependencies: `cvxpy` (Optimization), `networkx` (Topology), `scikit-learn` (GLasso/PCA), `yfinance` (Data).*

### **B. Automated Pipeline Flow**

1. **Data Synchronisation:** The script automatically retrieves daily closing prices for 76 JSE tickers.
2. **Liquidity & Density Filtering:** Assets are screened for 'stale' prices (zero-return days) to prevent numerical instability during matrix inversion.
3. **Rolling-Window Engine:** The backtest executes a recursive loop (90-day training/10-day testing). This ensures strict temporal separation, meaning parameters are never estimated using future data.
4. **Solver Stability:** The `CVXPY` optimizer is wrapped in exception-handling logic. If a specific window's covariance estimation fails to converge, the system defaults to a stable 1/N allocation to ensure the backtest remains runnable and continuous.

### **C. How to Verify the Output**

To verify that the full pipeline has executed as intended, check these specific technical checkpoints:
* **Verification Summary:** Check the console for the $p/n$ ratio (must be $>0.5$) and the factor purging count.
* **Top 10 Centrality Audit:** Review the table ranking JSE securities by structural influence. This confirms the Topological Penalty is targeting the correct assets.
* **Metric Horse Race Table:** Look for the 'TOPOLOGICAL PREDICTIVE POWER' output. This proves that Eigenvector centrality is a statistically significant predictor of risk in the JSE universe.
* **Kupiec Test Result:** Ensure the 'STATISTICAL VALIDATION OF RISK FORECASTS' displays a 'Pass' for the TR-FGL portfolio.
* **Ablation Study Plots:** Review the final charts comparing TR-FGL against standard FGL to see the wealth accumulation and drawdown reduction provided by the structural extension.

---

## **4. Step-by-Step Execution Guide**

This pipeline is designed for reproducibility. The script automatically handles data acquisition, cleaning, and optimization without requiring external datasets.

### **Step 1: Environment Setup**

Ensure you have a Python 3.9+ environment. It is recommended to use a virtual environment to avoid version conflicts with the optimization solvers.

```bash
# Install all required libraries
pip install -r requirements.txt

```

### **Step 2: Configuration & Parameters**

The global parameters are defined at the start of the notebook. The default configuration is tuned to replicate the high-dimensional stress conditions ($p \approx n$) analyzed in Lee & Seregina (2023):

* **Universe:** 76 JSE-listed tickers (Adjusted Closing Prices).
* **Training Window:** 90 Trading Days.
* **Rebalancing Step:** 10 Trading Days (Recursive).

### **Step 3: Sequential Execution**

Open `TR-FGL Codescript - Replication and Extension.ipynb` and execute the cells in order:

1. **Data Ingestion:** Running the first code block will sync live JSE data via `yfinance`.
2. **Section B (Replication):** This block performs the factor decomposition and calculates the full precision matrix. It will print a 'Verification Summary' to the console.
3. **Comparative Visualization:** This cell generates the replication figure. It plots the cumulative returns of the unpenalised GMV vs. standard FGL, providing the performance-related proof requested in the project feedback.
4. **Section C (Extension):** This block executes the Topologically Regularised (TR-FGL) model, calculating Eigenvector Centrality and solving the constrained optimization problem via `CVXPY`.

### **Step 4: Interpreting Results**

Upon completion, the script generates three primary audit outputs:

* **Metric Horse Race:** A statistical table showing which topological metric (Eigenvector vs. Betweenness) has the highest predictive power for realized JSE risk.
* **Kupiec Test Table:** A formal backtest for Value-at-Risk (VaR) integrity.
* **Cumulative Wealth Index:** A final chart showing the out-of-sample performance of the TR-FGL strategy against all benchmarks.

---

## **5. Operational Robustness (Model Risk)**

To ensure the repository is well-commented and runnable under all conditions:

* **Automated Data Scrubber:** The script automatically drops any asset that fails liquidity thresholds (e.g., if a JSE stock was suspended or delisted during the window).
* **Solver Fallback:** If the `ECOS` solver encounters a non-convexity or singularity in a specific rolling window, the code catch-block prevents a crash and logs the event, maintaining backtest continuity.
* **Technical Comments:** Every function includes 'Intuition' and 'Goal' headers to explain the financial logic to the reviewer.

---

## **6. Reference**
Lee, T.-H., & Seregina, E. (2023). Optimal Portfolio Using Factor Graphical Lasso. *Journal of Financial Econometrics*, 22(3). doi:10.1093/jjfinec/nbad011

---

## **6. Contributors**

* Maphoka Mahlomola
* Olonda Masuku
* Motlatso Moketla

---

