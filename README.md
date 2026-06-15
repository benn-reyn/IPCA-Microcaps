# A Remedy to Noise: Identifying and Adapting to Non-Linearity in Microcap Equities

Author: **Bennie Reynolds** *Department of Economics, Clemson University* *Date: April 24, 2026*

---

## Abstract
[cite_start]This repository contains the empirical asset pricing pipeline and machine learning architectures developed to evaluate the predictability of micro-cap equity excess returns[cite: 311]. [cite_start]Historically dismissed as idiosyncratic noise due to high volatility and liquidity frictions, this study demonstrates that micro-cap returns contain persistent signals when modeled using architectures capable of capturing non-linear and time-varying dynamics[cite: 311, 315, 316]. 

[cite_start]By systematically comparing traditional asset pricing models—Fama-French 5-Factor (FF5) and Instrumented Principal Component Analysis (IPCA)—against unstructured machine learning (XGBoost) and structured deep learning frameworks (Neural Autoencoder), we establish that a structured, non-linear architecture achieves superior out-of-sample performance and near-perfect portfolio decile monotonicity[cite: 311, 312].

---

## Theoretical Foundations for exploitable patterns within micro-cap segment
[cite_start]Traditional linear asset pricing models assume static, homogenous mappings from characteristics to factor loadings, an assumption that frequently fails in the micro-cap universe due to three key market frictions[cite: 323, 331, 332]:
1. [cite_start]**Limits to Arbitrage with Threshold Effects:** High bid-ask spreads (averaging 4–5%) imply that arbitrageurs deploy capital only when mispricings cross critical economic thresholds[cite: 339, 340, 341].
2. [cite_start]**Institutional Ownership Constraints:** Regulatory or internal screens (e.g., share price minimums like \$5.00) generate abrupt, step-function demand shifts[cite: 342, 343, 344].
3. [cite_start]**Retail Attention Asymmetries:** Retail investors dominate trading and display sharp, non-monotonic buying pressure in response to extreme tail events[cite: 345, 346, 347].

---

## Empirical Performance

[cite_start]The models were evaluated using individual stock-level cross-sectional predictions via monthly Out-of-Sample (OOS) Predictive $R^2$ (both Mean-Based and Zero-Based)[cite: 391, 393]. [cite_start]To confirm robustness against overfitting, a **noise reshuffling protocol** was applied to break feature-return correlations[cite: 393, 394].

| Model Class | Zero-Based OOS $R^2$ (%) | Mean-Based OOS $R^2$ (%) | Noise Reshuffled $R^2$ (%) | Performance Notes |
| :--- | :---: | :---: | :---: | :--- |
| **Fama-French 5-Factor (FF5)** | -1.0215% | -1.5674% | -1.1774% | [cite_start]Unable to navigate high idiosyncratic volatility[cite: 426, 428, 431]. |
| **Linear IPCA** | 0.1793% | 0.0416% | -0.4430% | [cite_start]Successfully extracts linear systemic risk; aligns with broad-market baselines[cite: 426, 432, 435]. |
| **XGBoost** | 0.0861% | 0.0747% | -0.0654% | [cite_start]Captures local splits and non-linearities, but absolute predictions are volatile[cite: 426, 437, 441]. |
| **Neural Autoencoder** | **0.3027%** | **0.2113%** | **-0.0987%** | [cite_start]**Top Performer.** Bends to fit saturation effects with strict ordinal monotonicity[cite: 426, 443, 484]. |

---

## Parameterization & Architecture

[cite_start]The architectures are hyperparameter-tuned to balance predictive capacity with strict generalization constraints in high-noise environments[cite: 418, 447, 448]:

* [cite_start]**Instrumented PCA (IPCA):** Linear mapping utilizing $K = 2$ latent factors, an expanding estimation window, and a standard Ridge penalty ($\lambda = 1.00$)[cite: 449, 450, 456].
* [cite_start]**XGBoost:** Unstructured tree learner optimized with `max_depth=2`, `n_estimators=100`, `learning_rate=0.05`, and a robust regularization blend ($L_1 = 1.0$, $L_2 = 0.1$)[cite: 449].
* [cite_start]**Neural Autoencoder:** Structured Deep Factor model composed of a multi-layer perceptron **Beta-Network** (Hidden Layers: 64 $\rightarrow$ 32) mapping 11 rank-standardized characteristics into $K = 5$ latent factors[cite: 417, 421, 422, 449]. [cite_start]Employs `ReLU` activations, 10% dropout, and a secondary ridge penalty ($\lambda = 10^{-3}$) for latent factor matrix inversion stability[cite: 449, 452, 455].

---

## Data Pipeline & Preprocessing

[cite_start]The underlying panel comprises 11 characteristics spanning January 1995 through December 2024, combining market feeds from **CRSP** and accounting fundamentals from **Compustat**[cite: 354, 355]:
* [cite_start]**Universe Definition:** Restricted to the 20th–5th percentile of U.S. Equities via NYSE market capitalization breakpoints to explicitly isolate the micro-cap space[cite: 326, 370].
* [cite_start]**Data Cleansing:** To combat extreme micro-cap outlier vulnerability, all continuous variables are winsorized at the 1% and 99% levels[cite: 372].
* [cite_start]**Missingness & Linkage:** Linked via an 8-digit CUSIP bridging mechanism (maintaining a robust ~84% historical coverage profile)[cite: 356, 365]. [cite_start]Financials are lagged by 6 months and forward-filled annually to prevent look-ahead bias[cite: 369].
* [cite_start]**Delisting Bias Mitigation:** A strict **-30% return penalty** is programmatically injected upon hitting CRSP delisting codes (500–584) to eliminate survivorship distortions[cite: 368].

---

## Repository Structure

```text
├── .gitignore                      # Prevents massive .parquet tracking (>250MB)
├── EDA.ipynb                       # Exploratory analysis and missingness tracking
├── pipeline.ipynb                  # Data parsing, winsorization, delisting and alignment logic
├── Autoencoder.ipynb               # Fitting and implementation of FF5, IPCA, XGBoost, Autoencoder models
├── requirements.txt                # System dependencies
└── README.md                       # Repository overview
