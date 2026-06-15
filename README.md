# A Remedy to Noise: Identifying and Adapting to Non-Linearity in Microcap Equities

Author: **Bennie Reynolds** *Department of Economics, Clemson University* *Date: April 24, 2026*

---

## Abstract
This repository contains the empirical asset pricing pipeline and machine learning architectures developed to evaluate the predictability of micro-cap equity excess returns. Historically dismissed as idiosyncratic noise due to high volatility and liquidity frictions, this study demonstrates that micro-cap returns contain persistent signals when modeled using architectures capable of capturing non-linear and time-varying dynamics. 

By systematically comparing traditional asset pricing models—Fama-French 5-Factor (FF5) and Instrumented Principal Component Analysis (IPCA)—against unstructured machine learning (XGBoost) and structured deep learning frameworks (Neural Autoencoder), we establish that a structured, non-linear architecture achieves superior out-of-sample performance and near-perfect portfolio decile monotonicity.

---

## Theoretical Foundations for exploitable patterns within micro-cap segment
Traditional linear asset pricing models assume static, homogenous mappings from characteristics to factor loadings, an assumption that frequently fails in the micro-cap universe due to three key market frictions:
1. **Limits to Arbitrage with Threshold Effects:** High bid-ask spreads (averaging 4–5%) imply that arbitrageurs deploy capital only when mispricings cross critical economic thresholds.
2. **Institutional Ownership Constraints:** Regulatory or internal screens (e.g., share price minimums like \$5.00) generate abrupt, step-function demand shifts.
3. **Retail Attention Asymmetries:** Retail investors dominate trading and display sharp, non-monotonic buying pressure in response to extreme tail events.

---

## Empirical Performance

The models were evaluated using individual stock-level cross-sectional predictions via monthly Out-of-Sample (OOS) Predictive $R^2$ (both Mean-Based and Zero-Based). To confirm robustness against overfitting, a **noise reshuffling protocol** was applied to break feature-return correlations.

| Model Class | Zero-Based OOS $R^2$ (%) | Mean-Based OOS $R^2$ (%) | Noise Reshuffled $R^2$ (%) | Performance Notes |
| :--- | :---: | :---: | :---: | :--- |
| **Fama-French 5-Factor (FF5)** | -1.0215% | -1.5674% | -1.1774% | Unable to navigate high idiosyncratic volatility. |
| **Linear IPCA** | 0.1793% | 0.0416% | -0.4430% | Successfully extracts linear systemic risk; aligns with broad-market baselines. |
| **XGBoost** | 0.0861% | 0.0747% | -0.0654% | Captures local splits and non-linearities, but absolute predictions are volatile. |
| **Neural Autoencoder** | **0.3027%** | **0.2113%** | **-0.0987%** | **Top Performer.** Bends to fit saturation effects with strict ordinal monotonicity. |

---

## Parameterization & Architecture

The architectures are hyperparameter-tuned to balance predictive capacity with strict generalization constraints in high-noise environments:

* **Instrumented PCA (IPCA):** Linear mapping utilizing $K = 2$ latent factors, an expanding estimation window, and a standard Ridge penalty ($\lambda = 1.00$).
* **XGBoost:** Unstructured tree learner optimized with `max_depth=2`, `n_estimators=100`, `learning_rate=0.05`, and a robust regularization blend ($L_1 = 1.0$, $L_2 = 0.1$).
* **Neural Autoencoder:** Structured Deep Factor model composed of a multi-layer perceptron **Beta-Network** (Hidden Layers: 64 $\rightarrow$ 32) mapping 11 rank-standardized characteristics into $K = 5$ latent factors. Employs `ReLU` activations, 10% dropout, and a secondary ridge penalty ($\lambda = 10^{-3}$) for latent factor matrix inversion stability.

---

## Data Pipeline & Preprocessing

The underlying panel comprises 11 characteristics spanning January 1995 through December 2024, combining market feeds from **CRSP** and accounting fundamentals from **Compustat**:
* **Universe Definition:** Restricted to the 20th–5th percentile of U.S. Equities via NYSE market capitalization breakpoints to explicitly isolate the micro-cap space.
* **Data Cleansing:** To combat extreme micro-cap outlier vulnerability, all continuous variables are winsorized at the 1% and 99% levels.
* **Missingness & Linkage:** Linked via an 8-digit CUSIP bridging mechanism (maintaining a robust ~84% historical coverage profile). Financials are lagged by 6 months and forward-filled annually to prevent look-ahead bias.
* **Delisting Bias Mitigation:** A strict **-30% return penalty** is programmatically injected upon hitting CRSP delisting codes (500–584) to eliminate survivorship distortions.

---

## Repository Structure

```text
├── .gitignore                      # Prevents massive .parquet tracking (>250MB)
├── EDA.ipynb                       # Exploratory analysis and missingness tracking
├── pipeline.ipynb                  # Data parsing, winsorization, delisting and alignment logic
├── Autoencoder.ipynb               # Fitting and implementation of FF5, IPCA, XGBoost, Autoencoder models
├── requirements.txt                # System dependencies
└── README.md                       # Repository overview
```
