## Project: Quantifying Market Efficiency via Entropy and Liquidity

### 1. Core Project Objective and Hypothesis

The central aim of this project is to:
- Have a quantifiable measure of **Market Efficiency** using **Permutation Entropy (PE)** applied to financial asset returns.
- Use **Brownian Motion (BM)** as a theoretical benchmark for maximum market efficiency.
- Measure **Market Illiquidity** using the **Amihud Illiquidity Ratio**.
- Hopefully, establish a statistical relationship between the **Permutation Entropy (PE)** of financial asset returns and an accepted measure of **Market Illiquidity**, specifically the **Amihud Ratio**.

The working hypothesis, derived from the **Efficient Market Hypothesis (EMH)**, is:

> **Markets with higher Permutation Entropy (indicating greater time series randomness and complexity) are more efficient and should hold greater similarity to a Brownian Motion. This efficiency should manifest as a lower degree of illiquidity (low Amihud Ratio). Therefore, a statistically significant negative correlation is expected between PE and the Amihud Ratio.**

---

### 2. Theoretical Metrics and Definitions

#### A. Permutation Entropy (PE) - The Efficiency/Complexity Metric

Permutation Entropy, based on the work of Bandt and Pompe (2002), is a robust non-parametric measure of the complexity and predictability of a time series. It quantifies the degree of disorder by comparing the frequency of local order patterns.

1.  **Ordinal Pattern (Embedding):** For a time series $\{x_t\}_{t=1}^N$, an embedding vector $\mathbf{x}_{t}^{(m, \tau)}$ is constructed:
    $$\mathbf{x}_{t}^{(m, \tau)} = (x_t, x_{t+\tau}, x_{t+2\tau}, \dots, x_{t+(m-1)\tau})$$
    where $m$ is the **embedding dimension (order)**, and $\tau$ is the **time delay (lag)**.

2.  **Permutation:** The vector $\mathbf{x}_{t}^{(m, \tau)}$ is mapped to a permutation $\pi = (\pi_0, \pi_1, \dots, \pi_{m-1})$ of the indices $(0, 1, \dots, m-1)$ such that the elements of the embedded vector are sorted:
    $$x_{t+\pi_0\tau} \le x_{t+\pi_1\tau} \le \dots \le x_{t+\pi_{m-1}\tau}$$

3.  **PE Formula:** The Permutation Entropy $H(m, \tau)$ is defined using the Shannon Entropy formula applied to the probabilities $P(\pi)$ of the $m!$ possible ordinal patterns ($\Pi$):
    $$H(m, \tau) = -\sum_{\pi \in \Pi} P(\pi) \ln(P(\pi))$$

4.  **Normalization:** The result is often normalized by its maximum possible value, $\ln(m!)$, to yield a value between 0 (perfect order) and 1 (maximum disorder/randomness).
    $$PE = \frac{H(m, \tau)}{\ln(m!)}$$

* **Implementation:** Calculated on **logarithmic returns** $r_t = \ln(P_t/P_{t-1})$. The project will use multiple $m$ values ($m=3, 5, 6$) to verify results robustness.

---

#### B. Brownian Motion (BM) - The Efficiency Benchmark

Simulated Brownian Motion (specifically, a Gaussian random walk or White Noise) acts as the theoretical perfectly efficient market where returns are independent and identically distributed (I.I.D.). This establishes the **Maximum Entropy Benchmark ($PE_{BM}$)**.

1.  **Simulated Series:** A White Noise series $W_t$ is generated with zero mean and a standard deviation $\sigma$ matching the average volatility of the financial returns being analyzed:
    $$W_t \sim \mathcal{N}(0, \sigma^2)$$

2.  **Benchmark Value:** The PE is calculated for this simulated series:
    $$PE_{BM} = \text{PE}(W_t, m, \tau)$$

* **Interpretation:** Real market PEs are compared to $PE_{BM}$. The closer $PE_{market}$ is to $PE_{BM}$, the closer the market is to theoretical maximum efficiency.

---

#### C. Amihud Illiquidity Ratio - The Illiquidity Metric

The Amihud Illiquidity Ratio (Amihud, 2002) measures the absolute daily price change per unit of trading volume, serving as a friction-based proxy for market efficiency.

1.  **Daily Ratio:** For a given day $t$, the daily Amihud ratio $A_t$ is:
    $$A_t = \frac{|R_t|}{V_t}$$
    where $|R_t|$ is the absolute daily return and $V_t$ is the dollar volume (Volume $\times$ Price).

2.  **Annual/Mean Ratio (The Project Metric):** The final Amihud metric for an asset across the entire observation period $T$ is the time-series average, typically scaled for interpretability:
    $$\text{Avg Amihud} = \frac{1}{T} \sum_{t=1}^{T} A_t$$

* **Interpretation:** A **higher Avg Amihud** value indicates that a small volume of trading causes a large price movement, signifying **higher illiquidity** and **lower efficiency**.

---

### 3. Methodology and Statistical Analysis

1.  **Data Curation:** Acquire daily Adjusted Close Prices and Volume for a large cross-section of assets. Calculate daily $\text{Log Returns}$ ($r_t$) and $\text{Dollar Volume}$ ($V_t$).
2.  **Metric Aggregation:** For each asset $i$:
    * Calculate $\text{PE}_{i, m}$ (using $m=3, 5, 6$)
    * Calculate $\text{Avg Amihud}_i$
3.  **Bivariate Correlation:** The core analysis involves calculating the **Pearson Product-Moment Correlation Coefficient ($\rho$)** between the vector of PE values ($\mathbf{PE}$) and the vector of Amihud values ($\mathbf{Amihud}$) across all successfully analyzed stocks:
    $$\rho(\mathbf{PE}, \mathbf{Amihud}) = \frac{\sum_{i=1}^{N} (\text{PE}_i - \overline{\text{PE}}) (\text{Amihud}_i - \overline{\text{Amihud}})}{\sqrt{\sum_{i=1}^{N} (\text{PE}_i - \overline{\text{PE}})^2 \sum_{i=1}^{N} (\text{Amihud}_i - \overline{\text{Amihud}})^2}}$$
    (Where $N$ is the number of assets, and $\overline{X}$ is the mean of the metric $X$).

* **Hypothesis Confirmation:** A result where $\rho$ is negative and the associated **p-value** is below the significance level (e.g., $p < 0.05$) will confirm the hypothesis that higher entropy markets are more liquid/efficient.

---

### 4. Further Analysis Extensions (Alternative Routes)

While the primary focus is on cross-sectional correlation, the project can be extended:

1.  **Alternative Entropy Measures:** Substitute PE with other complexity metrics like **Sample Entropy (SampEn)** or **Lempel-Ziv Complexity (LZC)** to see if the statistical relationship with Amihud remains consistent, thereby generalizing the finding beyond the specific PE measure.
2.  **Time-Series Analysis:** Instead of one mean value per stock, calculate **Rolling Permutation Entropy** (using a window of 252 days) and **Rolling Amihud Ratio** to analyze how the relationship evolves over time, especially during periods of market stress (e.g., crises).
3.  **Regression Modeling:** Conduct a formal **linear regression** where $\text{PE}$ is the dependent variable and $\text{Avg Amihud}$ (and potentially other factors like average volatility or market capitalization) are independent variables to test the specific explanatory power of liquidity on entropy.
