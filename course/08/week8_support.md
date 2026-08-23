# Week 8 — Support

This file contains preparation, a reproduction record, constraint checks, troubleshooting, optional extensions, and sources for [Week 8 main](week8_main.md).

## Before class

Bring the Week 6 transaction-cost convention and the Week 7 limits on performance claims. In a clean notebook, run the following check and preserve either the output or the full error message:

```python
import numpy as np
import pandas as pd

print("numpy", np.__version__)
print("pandas", pd.__version__)
```

The required exercise uses only artificial data. No market, ticker, data provider, or classroom package version has been approved for the later team project.

## Weighting-method record

```text
Method name:
Financial purpose:
Required inputs and units:
Input lookback:
Last information date used for each target:
Raw-input calculation:
Normalization rule:
Fallback, or explicit failure when normalization is impossible:
Full-investment constraint:
Long-only or short-selling rule:
Position cap:
Gross-exposure rule:
Target-weight date:
Return interval receiving the target:
Rule-setting period:
Evaluation period:
Rebalance schedule:
Target-to-holding drift rule:
Turnover convention:
Transaction cost and unit conversion:
Comparison methods and common conditions:
Concentration and performance evidence:
Main limitation:
Files and outputs preserved:
```

## Required checks

- Confirm the same asset names, order, dates, information boundary, cap, rebalance rule, and cost for all methods.
- Verify every raw input and final weight is finite.
- Verify every target vector sums to one within tolerance, is nonnegative, and respects the cap.
- Record an all-nonpositive signal event; do not silently apply an unreported fallback.
- Stop on missing or infinite input, nonpositive volatility, negative market capitalization, or an infeasible cap.
- Calculate weights using information through the previous business date, not the return being allocated.
- Distinguish each target vector from holdings that drift after asset returns.
- Calculate turnover against pre-trade holdings and recalculate it separately for every method.
- Use the same cost rate and identical evaluation dates for all methods.
- Report concentration and turnover with net results and preserve unsuccessful checks.
- Do not change a method after inspecting the separate evaluation period and still describe that period as unused.

## Troubleshooting

| Symptom | First check | Safe next action |
|---|---|---|
| Weights do not sum to one | Print the raw-input total and final sum at full precision | Stop before calculating returns and inspect normalization |
| A weight exceeds the cap | Check whether the cap is feasible and inspect each redistribution iteration | Correct the constraint calculation and rerun every weight check |
| Signal-based weights are missing | Check whether inputs are unavailable or all clipped signals equal zero | Stop on missing input; apply and record equal weights only for the declared all-zero fallback |
| Inverse-volatility weights are infinite | Inspect zero, missing, or nonpositive volatility estimates | Do not replace them silently; correct the lookback or stop the calculation |
| Evaluation uses its own return | Print `date`, `previous_date`, and the last input row | Restore the one-period information lag and rerun from the first cell |
| Turnover is unexpectedly zero | Compare the new target with the drifted holdings immediately before trading | Recalculate the method's own turnover rather than copying another method's series |
| Methods have unequal evaluation rows | Compare all four indices and missing values before aggregation | Restore the common date boundary before interpreting results |
| Final wealth is called proof of the best method | Inspect volatility, concentration, turnover, seed, period, and artificial-data status | Limit the sentence to the stated criterion and experiment |

## Optional extensions

- Compare caps of 0.40, 0.60, and 1.00 while holding every other assumption fixed.
- Compare monthly and quarterly rebalancing and recalculate each method's turnover and cost.
- Replace the trailing volatility estimate with an expanding estimate and record how the available information changes.
- Add short positions only after defining gross exposure, leverage, borrowing, and a new feasible normalization rule.
- Repeat the artificial experiment across prespecified seeds and show the distribution of differences rather than retaining only the most favorable path.

## Software documentation

- [`pandas.DataFrame.rolling`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html) defines the rolling-window operation used for trailing volatility.
- [`numpy.random.Generator.multivariate_normal`](https://numpy.org/doc/stable/reference/random/generated/numpy.random.Generator.multivariate_normal.html) documents the artificial-return generator.

## Financial and portfolio sources

- DeMiguel, V., Garlappi, L., & Uppal, R. (2009). [Optimal Versus Naive Diversification: How Inefficient is the 1/N Portfolio Strategy?](https://doi.org/10.1093/rfs/hhm075). *The Review of Financial Studies, 22*(5), 1915–1953.
- S&P Dow Jones Indices. [Methodology Matters](https://www.spglobal.com/spdji/en/research-insights/index-literacy/methodology-matters/) explains equal and market-capitalization index weighting, including the distinction between full and float-adjusted capitalization.
- Chaves, D. B., Hsu, J. C., Li, F., & Shakernia, O. (2012). [Efficient Algorithms for Computing Risk Parity Portfolio Weights](https://www.top1000funds.com/wp-content/uploads/2012/08/Efficient-algorithms-for-computing-risk-parity-portfolio-weights.pdf). SSRN 2117303.

DeMiguel et al.'s abstract and empirical comparisons support treating equal weights as a serious benchmark; they do not show that equal weighting always wins. The S&P page's weighting section supports the index-weight definitions, but today's artificial market capitalizations do not implement a real provider's eligibility, divisor, or float rules. The abstract and pages 1–2 of Chaves et al. distinguish the simple inverse-volatility calculation from a covariance-aware equal-risk-contribution calculation. The class does not reproduce any source's datasets, complete procedures, or empirical claims.
