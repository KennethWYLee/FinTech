# Week 4 — Support

This file contains the ledger dictionary, backtest record, troubleshooting, and extensions for [Week 4 main](week4_main.md).

## Before class

- Bring the Week 3 signal-time statement.
- Use `numpy` and `pandas`; no online data are required.
- The [older strategy and backtesting notebook](../resources/notebooks/03_TypeB_AI交易訊號_策略設計與回測.ipynb) is optional background. It also contains costs and performance topics assigned to later weeks, so it is not the required Week 4 sequence.

## Ledger dictionary

| Field | Meaning | Timing check |
|---|---|---|
| `asset_return` | Close-to-close artificial asset return ending on the row date | Uses prices at $t-1$ and $t$ |
| `signal_at_close` | Rule output calculated after the row's close | Cannot earn `asset_return` on the same row |
| `position` | Exposure held during the current return interval | Equals the previous signal after that signal becomes available; otherwise cash |
| `trade` | Absolute change in position | Equals zero without a position change |
| `strategy_return_gross` | Position multiplied by asset return | Excludes all costs |
| `strategy_wealth_gross` | Compounded gross strategy returns | Starts from one unit |

## Backtest record

```text
Data origin and date range:
Return interval:
Feature and lookback:
Signal rule:
Signal time:
Decision time:
Execution assumption:
Position set:
Cash-return assumption:
Short-selling and leverage rules:
Transaction costs included:
Benchmark:
Lag applied:
Final position and terminal-liquidation rule:
Assertions passed:
Known omissions:
Claim not supported:
```

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| Strategy return appears before feature history exists | Inspect signal initialization | State whether unavailable features lead to cash or missing positions |
| Same row contains signal and earned return | Inspect the position shift | Shift the signal according to the stated decision and execution times |
| `trade` is negative | Check whether a signed difference was used | Use the absolute position change defined in the Week 4 specification |
| First wealth value is missing | Inspect initialization of the first strategy return | Set a stated initial cash return for wealth construction only |
| Wealth becomes nonpositive | Inspect returns and leverage | Stop; a return at or below -100% violates this long-only artificial setup |
| Removing the lag improves results | Re-read the stated timing assumptions | Keep the valid lag; performance cannot repair invalid timing |
| The final row records an unexplained extra trade | Inspect terminal-liquidation code | Include liquidation only when the written strategy specification requires it |

## Optional extensions

- Add a one-period execution delay and compare the ledger, without costs.
- Permit positions in `{-1, 0, 1}` and state the short-selling assumption before calculating returns.
- Add a maximum position of 0.5 and keep the remainder in zero-return cash.
- Write a function that accepts price, signal, and lag and returns the audited ledger.

## Official software references

- [`pandas.DataFrame.shift`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.shift.html)
- [`pandas.DataFrame.diff`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.diff.html)
- [`pandas.DataFrame.cumprod`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.cumprod.html)

## Financial research reference

- Arnott, R., Harvey, C. R., & Markowitz, H. (2019). [A Backtesting Protocol in the Era of Machine Learning](https://doi.org/10.3905/jfds.2019.1.064). *The Journal of Financial Data Science, 1*(1), 64–74.

The paper motivates explicit, reproducible backtest design and cautious interpretation; this Week 4 artificial exercise does not reproduce its complete protocol. The software operations implement the stated classroom assumptions. Neither source decides whether the assumed execution is realistic; that judgment and a fillable real-data execution price must be documented separately.
