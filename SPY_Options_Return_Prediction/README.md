# Predicting Short-Horizon SPY Returns Using Options Market Signals

This project investigates whether simple signals derived from the SPY options market contain information about subsequent SPY returns.

## Overview

Using daily SPY option-chain data, I construct four options-market signals:

- Near-30-day at-the-money implied volatility
- Daily change in ATM implied volatility
- Put/call volume ratio
- 25-delta volatility skew

I evaluate their ability to predict next-day and five-day SPY returns using linear regression with chronological expanding-window out-of-sample validation. Forecasts are compared against a zero-return baseline.

The analysis also compares overlapping and non-overlapping five-day return windows to examine the robustness of apparent longer-horizon predictability.

## Main Findings

- The models show limited out-of-sample predictive performance for next-day SPY returns.
- Predictive performance appears stronger for overlapping five-day returns, but weakens substantially when evaluated using non-overlapping five-day windows.
- Adding volatility skew provides some incremental information at the five-day horizon, but does not establish robust out-of-sample return predictability.
- The results highlight the importance of target construction, temporal validation, and robustness checks when evaluating financial forecasting models.

## Methodology

The project emphasizes a small set of economically motivated features rather than extensive feature or model selection. Models are evaluated chronologically using expanding training windows, with training observations restricted to labels that would have been available at the beginning of each test period to avoid look-ahead bias.

## Tools

Python, Pandas, NumPy, Statsmodels, Matplotlib

## Repository

The Jupyter notebook contains the full workflow, including data cleaning, feature construction, exploratory analysis, out-of-sample evaluation, robustness checks, and discussion of limitations.

### Notebook

The complete analysis, including data-quality checks, feature construction, exploratory analysis, expanding-window validation, and the volatility-skew extension, is available in [`SPY_Options_Return_Prediction.ipynb`](./SPY_Options_Return_Prediction.ipynb).
