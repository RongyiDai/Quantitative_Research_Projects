# Quantitative Research Projects

This repository contains quantitative finance research projects focused on empirical modeling, financial data analysis, and out-of-sample evaluation. My goal is to apply statistical and mathematical methods to financial questions while emphasizing careful feature construction, validation, and interpretation.

## Predicting Short-Horizon SPY Returns Using Options Market Signals

### Research Question

Do simple signals extracted from the SPY options market contain information about subsequent one-day SPY returns?

This project investigates whether information embedded in option prices and trading activity can help predict next-day SPY returns.

### Data

The analysis uses daily SPY option-chain and underlying-price data for 2024.

Each option observation contains information including strike, expiration, option type, volume, open interest, implied volatility, and Greeks.

### Data Source and Reproduction

The historical SPY options data used in this project are obtained from the public options-dataset-hist repository. The dataset is not duplicated in this repository because of its size.

The source repository can be cloned with:

<git clone --depth 1 https://github.com/anahatsingh-ui/options-dataset-hist.git>

The analysis uses:

<options-dataset-hist/spy/options_2024.parquet
options-dataset-hist/spy/underlying_prices.parquet>

The notebook loads these files using:

<import pandas as pd

options = pd.read_parquet(
    "options-dataset-hist/spy/options_2024.parquet"
)

underlying = pd.read_parquet(
    "options-dataset-hist/spy/underlying_prices.parquet"
)>

After cloning the data repository, the notebook can be run sequentially from top to bottom to reproduce the feature construction, exploratory analysis, and model evaluation.

Data-quality note: During exploratory analysis, the supplied implied-volatility field was found to contain discretized values. The project therefore treats the provided IV as a coarse options-market signal rather than reconstructing implied volatility from option prices.

### Signals

I construct three primary options-market signals:

- **Near-30-day ATM implied volatility:** a measure of the market's pricing of uncertainty over approximately a one-month horizon.
- **Change in ATM implied volatility:** captures daily repricing of expected volatility.
- **Put/call volume ratio:** measures relative put and call trading activity using options with 8–60 days to expiration and absolute delta between 0.2 and 0.8.

I also construct **25-delta volatility skew** as an exploratory feature to measure the difference between downside put IV and upside call IV.

### Prediction Target

The prediction target is the subsequent close-to-close SPY return:

$$
r_{t+1} = \frac{P_{t+1}}{P_t} - 1.
$$

Options features observed on day \(t\) are used to predict the return from day \(t\) to the next trading day.

### Methodology

The primary forecasting model is an OLS regression:

$$
r_{t+1}=\beta_0 + \beta_1 IV_t + \beta_2 \Delta IV_t + \beta_3 PCR_t + \epsilon_{t+1}.
$$

To preserve the temporal structure of the data, I use **expanding-window out-of-sample validation** rather than a random train/test split.

The model is initially trained on January–June 2024 and evaluated sequentially from July through December. After each test month, the training window expands to incorporate the newly observed data.

Performance is compared against a simple **zero-return forecast**.

### Key Results

Across the July–December out-of-sample period:

| Metric | Primary Model | Zero-Return Baseline |
| --- | ---: | ---: |
| Mean Squared Error | $7.92\times10^{-5}$ | $7.96\times10^{-5}$ |
| MSE Improvement | ~0.58% | — |
| Prediction Correlation | ~0.10 | — |

The options-derived signals exhibit a weak positive out-of-sample relationship with subsequent SPY returns, but the improvement in forecast accuracy is small and varies substantially across individual months.

Adding 25-delta volatility skew does not improve out-of-sample performance: the extended model produces slightly higher MSE and lower prediction correlation than the primary three-feature specification. I therefore retain the simpler model.

### Main Takeaway

The results provide limited evidence that simple options-market signals contain information about next-day SPY returns. However, the magnitude of the forecasting improvement is small and unstable across time.

The project illustrates the difficulty of translating economically motivated financial signals into robust short-horizon return forecasts and highlights the importance of comparing predictive models against simple benchmarks using chronological out-of-sample evaluation.

### Limitations

Several limitations should be considered when interpreting the results:

- The dataset contains only one year of observations, limiting statistical power and coverage of different market regimes.
- Implied-volatility values in the source data are discretized, reducing the precision of volatility-based features.
- Precise intraday timestamps are unavailable, so the analysis tests statistical predictability rather than an immediately executable closing-price trading strategy.
- ATM volatility and 25-delta skew use nearest available contracts rather than full volatility-surface interpolation.
- Put/call volume can reflect hedging, spreads, volatility trading, and market-making activity in addition to directional positioning.
- Feature development and evaluation use the same 2024 dataset, so the results should not be interpreted as a fully independent test of a frozen research specification.

### Potential Extensions

With a longer and higher-quality options dataset, natural extensions include:

- Testing the frozen specification across multiple years and market regimes.
- Constructing exact constant-maturity volatility and interpolated delta-based skew.
- Investigating whether options signals better predict future realized volatility or tail risk than the conditional mean of next-day returns.

### Notebook

The complete analysis, including data-quality checks, feature construction, exploratory analysis, expanding-window validation, and the volatility-skew extension, is available in [`SPY_Options_Return_Prediction.ipynb`](./SPY_Options_Return_Prediction.ipynb).
