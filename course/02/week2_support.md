# Week 2 — Support

This file contains preparation, records, terminology, detailed checks, troubleshooting, optional extensions, and sources for [Week 2 main](week2_main.md). The twenty teaching issues, four required practice issues, formulas, examples, and required evidence remain in the main file.

## Before class

Use Google Colab or a local Jupyter environment with Python, `numpy`, and `pandas`. The [Week 2 executable lesson notebook](../resources/notebooks/05_Week2_Financial_Data_and_Returns.ipynb) optionally imports `yfinance` for the source-and-retrieval example, but its required calculations remain executable without a live download. The preparation notebook also uses `matplotlib` for a figure check. If needed, run the [Colab environment and data check](../resources/notebooks/00_Colab_課前環境與資料檢查.ipynb).

Run the executable lesson from top to bottom. Preserve predictions before each example, visible outputs, revisions, discussion responses, and the requested evidence. Do not publish downloaded Yahoo Finance rows or saved notebook output containing those rows unless their permitted use and redistribution have been verified.

The preparation notebook uses fixed artificial data and does not download market data. Before class, confirm that you can:

- create and run a notebook from its first cell to its last cell;
- import `numpy` and `pandas`;
- display a `DataFrame` and its `dtypes`;
- save or export the notebook with visible outputs; and
- preserve the complete error message if an environment check fails.

The common market, assets, period, provider fields, currency, and redistribution conditions remain pending. Do not substitute an online dataset into required Week 2 work unless the instructor publishes those decisions.

## Data dictionary for the worked example

The descriptions below apply only to the artificial worked example. A real provider's documentation replaces them when real data are approved.

| Field | Meaning in the artificial example | Required check |
|---|---|---|
| `Date` | Artificial session-date label in `YYYY-MM-DD` format | Parse strictly; verify uniqueness and ascending order; do not infer a real exchange or time zone |
| `Open` | First artificial price included in the session | Positive and between `Low` and `High` |
| `High` | Largest artificial included price | Positive and not below `Open`, `Close`, or `Low` |
| `Low` | Smallest artificial included price | Positive and not above `Open`, `Close`, or `High` |
| `Close` | Final artificial included price | Positive and between `Low` and `High` |
| `Analysis_Price_A` | Artificial price used to calculate Asset A returns | Positive, nonmissing, and consistently defined across rows |
| `Volume` | Artificial number of traded units included in the session | Nonnegative; unit must be documented before real-data use |
| `Analysis_Price_B` | Artificial price used to calculate Asset B returns | Positive, nonmissing, and aligned to the same artificial session dates |

The worked example contains no split or cash dividend. Corporate-action calculations in Issues 6–7 and Practice issue 1 are separate artificial cases.

## Data-processing record

Complete the record with facts visible in the source or notebook. Use `pending` when a required fact is not yet known. Do not write `none` unless the item was checked and none was found.

```text
Provider and dataset:
Retrieval date and query or file identifier:
Redistribution status:
Artificial or real:
Asset identifier:
Asset name and security type:
Market or venue:
Currency and price unit:
Date range:
Timestamp meaning and time zone:
Observation frequency:
OHLCV session definition:
Price or return field selected:
Provider definition of that field:
Corporate actions and distributions included:
Return formulas and reporting units:
Date parsing and sorting rule:
Duplicate result and action:
Missing-value result and action:
Invalid or extreme-value result and action:
Trading-calendar evidence:
Frequency-conversion rule by field:
Multi-asset alignment rule:
Portfolio-weight assumption:
Automated checks executed:
Files and outputs preserved:
Claim supported by this evidence:
Claim not supported by this evidence:
Unresolved questions:
```

## Required audit checks

Preserve the output of every applicable check. A passed assertion does not replace the source record or financial interpretation.

### Identity and metadata

- Each asset has an identifier, name or artificial label, security type, market, and currency entry.
- Provider, dataset, retrieval information, and redistribution status are present.
- Every field has a definition and unit.
- Artificial labels are not presented as real assets or markets.

### Dates and intervals

- Dates parse under an explicit format.
- The index is unique and sorted before returns are calculated.
- Timestamp meaning and time zone are recorded or explicitly pending.
- Calendar gaps are classified using market evidence rather than weekday assumptions alone.
- Multi-asset returns share common start and end boundaries.

### Values and corporate actions

- Prices required by the Week 2 formulas are finite and positive.
- Volume is nonnegative and its unit is known or pending.
- OHLC range relations are checked.
- Missing, repeated, stale, or extreme values remain visible until a documented decision is made.
- Splits, dividends, and adjusted fields are not counted twice.

### Returns and aggregation

- The first return remains missing.
- Simple and log returns reconcile within numerical tolerance.
- Compounded simple return agrees with the endpoint ratio when the assumptions permit that identity.
- The geometric mean reproduces terminal wealth over the same nonmissing periods.
- Frequency conversion uses an aggregation appropriate to each field.
- Portfolio weights are finite, nonnegative, sum to one, and multiply same-interval asset returns.

## Terminology support

| Term | Meaning used in Week 2 |
|---|---|
| price level | A price observation with an asset, time, field, currency, and unit |
| price return | Relative price change over a stated holding interval |
| holding-period return | Return over one stated interval, including the distributions explicitly included by its convention |
| simple return | $P_t/P_{t-1}-1$ for a consistently defined price series |
| log return | $\ln(P_t/P_{t-1})$ for positive prices |
| cumulative return | Compounded change across consecutive holding intervals |
| wealth index | Growth of one initial unit under the stated return sequence |
| arithmetic mean return | Sum of observed returns divided by their count |
| geometric mean return | Constant per-period return that reproduces the compounded terminal wealth |
| adjusted price | Provider-defined price series adjusted for specified events |
| total-return series | Provider-defined series that includes specified distributions under a stated convention |
| trading calendar | The sessions and special schedules defined for a particular market |
| observation frequency | Interval or sampling rule represented by consecutive observations |
| common holding interval | Shared start and end boundaries for returns being combined |
| portfolio return | Weighted sum of same-interval asset returns under stated weights |

## Troubleshooting

| Symptom | First check | Safe next action |
|---|---|---|
| `ModuleNotFoundError` | Read the missing package name | Install only the required package in the current environment, restart if needed, and rerun from the first cell |
| `ValueError` while creating a `DataFrame` | Compare values per record with the column count | Correct the source record before calculating |
| Date index remains `object` | Display `dtypes` and the parsing cell | Use `pd.to_datetime` with the expected format and `errors="raise"` |
| Duplicate timestamp | Display all rows sharing that timestamp and their identifiers | Preserve them and investigate the source key before selecting or removing a row |
| First return is `NaN` | Check whether an earlier price exists | Keep it missing in the return series |
| Zero return appears beside a missing price | Inspect fills before `pct_change` | Remove automatic filling and document the missing-value decision |
| Log return is infinite or missing unexpectedly | Display the two prices in the ratio | Stop on zero, negative, missing, or misaligned prices |
| Simple and log returns do not reconcile | Check order, alignment, and units | Recalculate both from the same two price observations |
| Wealth endpoint identity fails | Compare first price, last price, and included returns | Repair interval or compounding logic before interpreting the result |
| Weekly return differs from compounded daily return | List both weekly endpoints and included daily intervals | Align endpoints and use last observed analysis prices before calculating returns |
| Weekly OHLCV looks implausible | Inspect the aggregation used for each field | Use first, maximum, minimum, last, or sum only when it matches the field definition |
| A portfolio return appears on the first price date | Inspect component returns and `min_count` | Require every component return; do not treat missing as zero |
| Assets have different return horizons | Display start and end prices for each component | Align common price endpoints first and then recalculate returns |
| Return is 100 times too large | Inspect decimal-to-percent conversion | Convert to percent once, only for reporting |
| Assertion fails | Read the variables and rows used by that assertion | Preserve the failure and diagnose the data or formula; do not delete the assertion |

## Optional extensions

These activities are not required for Week 2 completion:

- Add a third artificial asset in another currency and list the additional foreign-exchange information needed before forming one investor-currency portfolio return.
- Compare weekly OHLCV aggregation with an intentionally incorrect column-wise mean and explain every affected field.
- Introduce an extreme artificial return and prepare two competing explanations: a real event and a data or corporate-action error. State the evidence needed to distinguish them.
- Compare a price-only wealth index with a separately specified dividend-inclusive artificial return series without treating either as a provider's adjusted field.
- Create a normalized-price chart that starts each artificial asset at 1 and label the period, frequency, units, and artificial-data status.

## Official software references

- [`pandas.to_datetime`](https://pandas.pydata.org/docs/reference/api/pandas.to_datetime.html) parses date-like values.
- [`pandas.Series.pct_change`](https://pandas.pydata.org/docs/reference/api/pandas.Series.pct_change.html) calculates fractional change; its result is not automatically a percentage.
- [`pandas.DataFrame.resample`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.resample.html) groups time-series observations by a stated frequency before an aggregation is applied.
- [pandas time-series documentation](https://pandas.pydata.org/docs/user_guide/timeseries.html) documents time indexes, frequency conversion, and time-zone operations.
- [`numpy.log`](https://numpy.org/doc/stable/reference/generated/numpy.log.html), [`numpy.log1p`](https://numpy.org/doc/stable/reference/generated/numpy.log1p.html), and [`numpy.expm1`](https://numpy.org/doc/stable/reference/generated/numpy.expm1.html) support the log-return reconciliation used in the lesson.

## Financial and market references

- Campbell, J. Y., Lo, A. W., & MacKinlay, A. C. (1997). *The Econometrics of Financial Markets*. Princeton University Press, Chapter 1, “Prices, Returns, and Compounding,” pp. 9–13. [Publisher chapter record](https://doi.org/10.1515/9781400830213-005).
- The U.S. Securities and Exchange Commission's Investor.gov pages on [stock splits](https://www.investor.gov/introduction-investing/investing-basics/glossary/stock-split), [dividends](https://www.investor.gov/introduction-investing/investing-basics/glossary/dividend), and [investment returns](https://www.investor.gov/introduction-investing) support the basic split, distribution, and compound-growth distinctions used in the artificial examples.
- Nasdaq's [trading schedule](https://www.nasdaq.com/market-activity/stock-market-holiday-schedule) illustrates that a real market calendar includes holidays, special closes, session hours, and a stated time zone. It is not the calendar used by the artificial Week 2 dates.

The textbook supports standard return and compounding notation. Investor.gov supports the economic descriptions of splits and dividends. Nasdaq and pandas documentation support calendar and timestamp implementation examples. None defines the construction, correction history, or redistribution rights of a future course data provider; those items remain pending until the instructor approves a dataset.
