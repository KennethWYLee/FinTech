# Week 2 — Support

This file contains preparation, the detailed data dictionary, a processing-record template, terminology support, troubleshooting, and optional extensions for [Week 2 main](week2_main.md). The required calculations and activities remain in the main file.

## Before class

Use Google Colab or a local Jupyter environment with Python, `numpy`, and `pandas`. The linked preparation notebook also requires `matplotlib` for its figure check. If you need to check the environment, run the [Colab environment and data check](../resources/notebooks/00_Colab_課前環境與資料檢查.ipynb).

The preparation notebook deliberately uses fixed artificial data and does not offer an online download. An online download is not part of the required Week 2 evidence because the common market, asset, period, field definition, and redistribution conditions have not yet been specified in the course materials.

Before class, confirm that you can:

- create and run a notebook from the first cell to the last cell;
- import `numpy`, `pandas`, and `matplotlib` in the preparation notebook;
- save or export the notebook with its visible outputs; and
- preserve an error message or screenshot if the environment check fails.

## Data dictionary

The Week 2 main file uses a small artificial table. These field descriptions explain how each column is used in the exercise; they are not a substitute for a real provider's documentation.

| Field | Meaning in the Week 2 artificial data | Required check |
|---|---|---|
| `Date` | Trading-session label in `YYYY-MM-DD` format | Parse to `DatetimeIndex`; verify uniqueness, ascending order, and no weekend rows under the artificial-data convention |
| `Open` | First artificial transaction price for the session | Must be positive and lie between `Low` and `High` |
| `High` | Highest artificial price for the session | Must be positive and at least as large as `Open` and `Close` |
| `Low` | Lowest artificial price for the session | Must be positive and no larger than `Open` and `Close` |
| `Close` | Final artificial transaction price for the session | Must be positive and lie between `Low` and `High` |
| `Adj_Close` | Artificial analysis price for Asset A | Must be positive and nonmissing; no corporate action occurs in this artificial sample |
| `Volume` | Artificial number of units traded during the session | Must be nonnegative; not used to calculate Week 2 returns |
| `Asset_B_Adj_Close` | Artificial analysis price for Asset B | Must be positive and nonmissing; used only for the fixed-weight portfolio-return exercise |

For real data, the meaning of an adjusted-price field is provider-specific. Record the provider's exact definition rather than copying the Week 2 artificial-data description.

## Data-processing record

Complete this template using facts visible in your notebook. Do not write `none` unless you actually checked the item.

```text
Data origin:
Artificial or real:
Date range:
Observation frequency:
Price field used for returns:
Corporate-action definition checked:
Reason for selecting that field:
Return definitions:
Date parsing rule:
Duplicate-date result and action:
Missing-value result and action:
Trading-calendar assumption:
Frequency-conversion rule:
Portfolio-weight assumption:
Automated checks executed:
Files or outputs preserved:
Claim supported by this evidence:
Claim not supported by this evidence:
```

## Terminology support

| Term | Meaning in Week 2 |
|---|---|
| price level | An asset value at one observation time |
| simple return | Relative price change, $P_t/P_{t-1}-1$ |
| log return | Log price change, $\ln(P_t/P_{t-1})$ |
| cumulative return | Compounded change over consecutive periods |
| wealth index | Growth of one initial unit through a return sequence |
| adjusted price | A provider-defined price series adjusted for specified events |
| trading calendar | The sessions on which a particular market can trade |
| data frequency | The interval represented by consecutive observations |
| portfolio return | Weighted sum of asset returns for the same holding interval |

## Troubleshooting

| Symptom | First check | Action |
|---|---|---|
| `ModuleNotFoundError` | Identify the missing package in the error | Install only that package in the current environment, restart if required, and rerun from the first cell |
| `ValueError` while creating the DataFrame | Compare values per record with the number of column names | Correct the mismatched record before any calculation |
| Date index remains `object` | Run `prices.index.dtype` and inspect the date-conversion cell | Use `pd.to_datetime` before setting and sorting the index |
| `KeyError: 'Adj_Close'` | Print `prices.columns.tolist()` | Use the exact column name; do not silently switch to another price field |
| First return is `NaN` | Check whether an earlier price exists | Keep it missing for return analysis; it is expected |
| Unexpected zero return beside a missing price | Check whether a fill operation occurred before `pct_change` | Remove the automatic fill and document the missing-value decision |
| Weekly result is empty or unexpected | Verify a `DatetimeIndex`, sorted dates, and at least two weekly endpoints | Repair the index or extend the artificial sample; do not invent observations |
| Portfolio result appears on the first date | Inspect `sum(..., min_count=...)` | Require all asset returns before calculating the portfolio return |
| Assertion fails | Read the variables used in that assertion | Stop and diagnose the calculation; do not delete the assertion merely to continue |

## Optional extensions

These activities are not required for Week 2 completion:

- Change one artificial adjusted price and predict which simple returns, log returns, weekly return, and wealth-index values must change before rerunning the code.
- Introduce one exact duplicated row and compare `duplicated()` with `index.is_unique` after restoring the date index.
- Compare the compounded return with the arithmetic sum after increasing the artificial daily volatility.
- Add a third artificial asset and verify that the portfolio weights sum to one before calculating its one-period return.
- Create a normalized-price chart for both artificial assets, starting each at 1, and explain why this comparison is different from comparing raw price levels.

## Official software references

- [`pandas.to_datetime`](https://pandas.pydata.org/docs/reference/api/pandas.to_datetime.html) converts date-like inputs to pandas datetime objects.
- [`pandas.Series.pct_change`](https://pandas.pydata.org/docs/reference/api/pandas.Series.pct_change.html) calculates fractional change; multiply by 100 only when expressing the result as a percentage.
- [`pandas.DataFrame.resample`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.resample.html) performs time-series frequency conversion and requires a datetime-like index unless a datetime-like column is specified.
- [`pandas.DataFrame.sort_index`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_index.html) sorts a DataFrame by its index.
- [`numpy.log`](https://numpy.org/doc/stable/reference/generated/numpy.log.html) calculates the natural logarithm used in the log-return formula.
- [`numpy.expm1`](https://numpy.org/doc/stable/reference/generated/numpy.expm1.html) calculates $\exp(x)-1$, which is used to check the relationship between log and simple returns.

## Financial references

- Campbell, J. Y., Lo, A. W., & MacKinlay, A. C. (1997). *The Econometrics of Financial Markets*. Princeton University Press, Chapter 1, “Prices, Returns, and Compounding,” pp. 9–13. [Publisher chapter record](https://doi.org/10.1515/9781400830213-005).
- The U.S. Securities and Exchange Commission's [Investor.gov stock-split glossary](https://www.investor.gov/introduction-investing/investing-basics/glossary/stock-split) explains how a split changes the number of shares without changing shareholders' equity and provides a two-for-one numerical example.

The book supports standard return and time-series notation, and the Investor.gov page supports the split mechanism used in the artificial example. Software references document function behavior. None defines the corporate-action treatment or legal redistribution conditions of a market-data provider.
