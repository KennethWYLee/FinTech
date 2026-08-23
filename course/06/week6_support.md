# Week 6 — Support

This file contains the cost record, benchmark questions, troubleshooting, and extensions for [Week 6 main](week6_main.md).

## Before class

Bring the Week 4 backtest record and Week 5 update rules. Confirm that a clean notebook imports `numpy` and `pandas`; Week 6 does not require online market data.

## Cost and execution record

```text
Currency and return units:
Commission convention:
Quoted spread and conversion to half-spread:
Slippage assumption:
One-way total cost in basis points:
Turnover definition:
Entry cost treatment:
Exit cost treatment:
Terminal-liquidation rule:
Signal time:
Execution time:
Additional delay tested:
Cash-return assumption:
Benchmarks and the question each answers:
Cost range tested:
Conditions under which the comparison changes:
Liquidity or market-impact omission:
```

## Benchmark questions

| Benchmark | Question answered | Important limitation |
|---|---|---|
| Cash | Was risk-taking rewarded under the stated cash return? | Cash rate is assumed rather than estimated here |
| Buy-and-hold | Did timing improve on continuous exposure to the same asset? | Exposure and turnover differ |
| Simple transparent rule | Did the candidate improve on a lower-complexity timing rule? | Both may fail in the same market condition |
| Equal-weighted assets | Did allocation improve on a simple multi-asset allocation? | Requires a common asset universe and rebalance rule |

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| Cost is 100 times too large | Check basis-point conversion | Divide basis points by 10,000 once |
| Exit has no cost | Inspect absolute position difference | Charge every one-way change under the stated convention |
| Net wealth exceeds gross wealth | Inspect cost signs and turnover | Costs must be nonnegative deductions |
| Cost sensitivity is not decreasing | Hold signals and positions fixed across the grid | Change only the cost parameter |
| Delayed strategy is identical | Count different positions | Verify the additional shift and sufficient signal changes |
| Benchmark uses different dates | Compare nonmissing masks | Align all returns before comparison |

## Optional extensions

- Add a nonzero daily cash return and document its source or artificial construction.
- Make slippage increase with turnover and clearly label it an artificial impact rule.
- Compare daily and weekly rebalancing with the same signal.
- Calculate the exact break-even cost algebraically when total turnover is positive, then verify it numerically.

## Supporting resource

The [older backtesting notebook](../resources/notebooks/03_TypeB_AI交易訊號_策略設計與回測.ipynb) is optional because it combines several current weeks.

## Official software references

- [`pandas.DataFrame.diff`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.diff.html)
- [`pandas.Series.is_monotonic_decreasing`](https://pandas.pydata.org/docs/reference/api/pandas.Series.is_monotonic_decreasing.html)

## Financial research reference

- Patton, A. J., & Weller, B. M. (2020). [What You See Is Not What You Get: The Costs of Trading Market Anomalies](https://doi.org/10.1016/j.jfineco.2020.02.012). *Journal of Financial Economics, 137*(2), 515–549.

The paper supports treating implementation costs as material to claims about strategy returns. Week 6 uses a deliberately simplified linear cost deduction on artificial data; it does not reproduce the paper's estimation method or provide a real-market cost estimate.
