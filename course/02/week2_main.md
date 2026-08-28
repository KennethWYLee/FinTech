# Week 2 — Financial Data and Returns

## Core financial question

What must be known, checked, and calculated before a table of financial prices can support a valid return calculation?

Week 1 introduced the course workflow. Week 2 establishes the financial-data and return foundation for that workflow. Week 3 will ask when a variable became available, calculate technical indicators and financially motivated features, and convert a stated feature rule into a signal. Week 2 stops before those questions: it does not build a trading signal, fit a prediction model, or report backtest performance.

Use the [Week 2 executable lesson notebook](../resources/notebooks/05_Week2_Financial_Data_and_Returns.ipynb) with this document. The notebook expands every issue into a financial context, executable example, output interpretation, four discussion questions, evidence requirement, and first diagnostic action. Issue 2 includes an optional yfinance retrieval example; all required calculations continue with fixed artificial data when live access is unavailable.

## Observable outcome

After completing Week 2, you will produce an audited financial-data record and a reproducible return notebook. The evidence will identify the asset, market, fields, units, currency, timestamps, observation frequency, corporate-action assumptions, data problems, return definitions, frequency conversion, and common holding intervals. It will include simple and log returns, compounded returns, a wealth index, and one-period fixed-weight portfolio returns.

All numerical data in this lesson are artificial. They support calculation and auditing practice only. They do not describe a real security, document a real provider, or support an investment recommendation.

## Teaching objectives

After completing Week 2, you should be able to:

- identify what a financial-data row and field claim to measure;
- distinguish raw, closing, adjusted, and total-return information without assuming that provider labels are universal;
- calculate and reconcile simple returns, log returns, compounded returns, geometric mean returns, and a wealth index;
- audit asset identity, units, currency, timestamps, duplicates, missing values, invalid values, trading-calendar assumptions, and observation frequency;
- align multiple assets over common holding intervals and calculate a one-period fixed-weight portfolio return; and
- preserve reproducible evidence while limiting conclusions to the data and assumptions actually checked.

## Before you begin

Use a browser-based or local Python notebook with `numpy` and `pandas`. Run code from top to bottom. If the environment is not ready, follow [Week 2 Support](week2_support.md#before-class). Use only the artificial data in this document for required work. A real data source, asset universe, and period have not yet been approved for the course.

The twenty issues below are connected. A field decision changes the return definition; a date or unit error changes the holding interval; and an invalid holding interval makes a portfolio calculation uninterpretable even when the arithmetic is correct.

## Issue 1 — What does one financial-data row represent?

A row is an observation under a stated convention. It is not automatically a transaction or a complete trading session, and the row label alone does not specify publication availability. At minimum, identify the asset, market or venue, observation timestamp, field, unit, currency, and observation frequency. A row labeled only `2026-01-05, 100` is incomplete because `100` could be an opening price, closing price, adjusted series, index level, or another quantity. Week 3 separately examines when an observation became available.

Consider these two artificial descriptions:

```text
Record A: 2026-01-05, 100
Record B: Asset A, artificial market, 2026-01-05 session close,
          closing price, 100 artificial currency units per share, daily
```

Record B is more interpretable, although it still does not prove that the value is correct.

Checkpoint: write the missing metadata for Record A. If you cannot state the field and unit, do not calculate a return from it.

## Issue 2 — Which source and retrieval record support the data?

A reproducible analysis needs a source record even when the data are downloaded by code. Record the provider, dataset or endpoint, retrieval date, file or query identifier, applicable version, and permitted redistribution scope. A URL alone is insufficient when the returned data depend on query parameters or later revisions.

For the artificial data in this lesson, the source record is:

```text
Provider: instructor-created artificial example
Location: included directly in week2_main.md
Version: repository version of week2_main.md
Retrieval date: not applicable to an embedded fixed table
Market and asset: artificial
Redistribution: public course example
Real-market claim permitted: no
```

Before using a real dataset later, replace every artificial entry with provider evidence. If a classmate cannot determine which exact file or query created a table, the source record is incomplete.

Checkpoint: compare a URL-only citation with the artificial source record and list the additional entries needed for reproduction and redistribution review.

## Issue 3 — Have you identified the asset rather than only a short symbol?

A short symbol is not a complete asset identity. Preserve the provider's identifier, asset name, security type, market or venue, and currency. If the analysis combines multiple files, confirm that the same label refers to the same instrument in every file. Do not silently treat a stock, fund, index, futures contract, or cryptocurrency as interchangeable merely because each has a price column.

In this lesson, `Asset_A` and `Asset_B` are artificial identifiers. They have no issuer, exchange, or investable meaning. The identifiers allow calculations to be reproduced; they do not permit a claim about a real asset.

Checkpoint: create one row per asset in the [data-processing record](week2_support.md#data-processing-record). If market, security type, or currency is unknown, record `pending` instead of guessing.

## Issue 4 — What do Open, High, Low, Close, and Volume mean?

OHLCV fields summarize observations under a provider's session convention:

- `Open` is the first included price;
- `High` and `Low` are the largest and smallest included prices;
- `Close` is the final included price; and
- `Volume` is the reported traded quantity under the provider's unit convention.

These descriptions still require provider documentation. A regular-session close and a close that includes an extended session need not describe the same interval. Volume may count shares, contracts, or another unit.

For one artificial row, `Open=100`, `High=103`, `Low=99.5`, and `Close=102` satisfy the basic range check because the open and close lie between the low and high. If `High=101`, at least one field is inconsistent. The first diagnostic action is to display the complete source row; do not repair one value merely to make the check pass.

Checkpoint: write the three OHLC range relations you would test and one provider question that those arithmetic checks cannot answer.

## Issue 5 — Which price field answers the return question?

A raw closing price may be appropriate for a narrowly defined price-change calculation. A provider-defined adjusted series may be appropriate when its documented construction matches the economic return being studied. A total-return series may include distributions under a stated reinvestment convention. These fields are not interchangeable.

The decision must be written as a question and an assumption:

```text
Question: What change in value am I measuring?
Selected field: ______
Provider definition checked: ______
Included corporate actions or distributions: ______
Excluded items: ______
```

Never infer the construction of a column only from a label such as `Adj_Close`. If the provider definition is unavailable, state that the field cannot yet support the intended holding-return claim.

Checkpoint: complete the four-line field decision above for one artificial price-change question and identify the evidence that remains unavailable for a real provider.

## Issue 6 — How does a stock split affect price and share count?

Suppose one artificial share is priced at 100 before a two-for-one split. Ignoring market movement, taxes, fees, and rounding, the investor then owns two shares priced at 50. The position value is 100 before and after the split, but an unadjusted one-share price calculation gives

$$
\frac{50}{100}-1=-0.50.
$$

The negative 50% is a change in the price per share after the unit count changed. It is not the economic holding return for this example. A provider may adjust historical prices or provide another return field, but its documentation determines the exact treatment.

Checkpoint: state separately the before-and-after share count, price per share, and total position value. If the position value changes only because the share count was omitted, the comparison is not yet valid.

## Issue 7 — How can a cash dividend change the return calculation?

Price change alone omits a cash dividend received during a holding interval. Under a simplified convention with one cash dividend $D_t$, no taxes or fees, and no additional cash flows, the holding-period return is

$$
r_t^{H}=\frac{P_t-P_{t-1}+D_t}{P_{t-1}}.
$$

If a price falls from 100 to 99 and the holder receives a dividend of 2 during the interval, the price return is -1%, while the simplified holding-period return is

$$
\frac{99-100+2}{100}=0.01=1\%.
$$

This example does not define how a particular provider adjusts prices or reinvests dividends. Confirm the ex-dividend, payment, adjustment, and reinvestment conventions before applying a real field. If the calculated return includes both a dividend and a price series that already incorporates that dividend, the distribution may have been counted twice.

Checkpoint: for an artificial change from 60 to 59 with a cash dividend of 2, calculate the price return and the simplified holding-period return, then state the dividend-timing assumption.

## Issue 8 — Are units, currency, and percentage language consistent?

A price has a unit, such as currency units per share. A simple or log return is dimensionless. Multiplying a decimal return by 100 expresses it as a percent. A percentage point is the arithmetic difference between two percentages, not another name for a percent change.

For example, a return rising from 2% to 3% increases by 1 percentage point and by 50% relative to the original 2%. These statements answer different questions.

Returns on assets denominated in different currencies cannot be interpreted as one investor's portfolio return until the investor currency and foreign-exchange treatment are stated. Week 2 will not add foreign-exchange returns; it will reject an undocumented currency mixture. If a value appears 100 times too large, first check whether a decimal return was converted to percent more than once.

Checkpoint: express `0.025` as a decimal return and a percent return, and explain why a change from 2% to 3% is one percentage point rather than one percent.

## Issue 9 — How is a simple return calculated?

Let $P_t$ and $P_{t-1}$ be consistent price observations for the end and beginning of one holding interval. The simple return is

$$
r_t=\frac{P_t}{P_{t-1}}-1.
$$

If the price rises from 100 to 102, then $r_t=0.02=2\%$. If it falls from 100 to 98, then $r_t=-0.02=-2\%$. Reversing the two prices changes both the interval direction and the denominator.

The first return in a finite price table is missing because no earlier price is present. Do not replace it with zero when estimating return statistics. If `pct_change` reports a zero next to a missing price, first check whether an automatic fill occurred.

Checkpoint: calculate the simple return from 98 to 101, label its start and end, and record the result as both a decimal and a percent.

## Issue 10 — How is a log return calculated?

When both prices are positive, the log return is

$$
g_t=\ln\left(\frac{P_t}{P_{t-1}}\right)=\ln(1+r_t).
$$

For the change from 100 to 102,

$$
g_t=\ln(1.02)\approx0.019803.
$$

Log returns add across consecutive intervals. They are not percentages until a reporting conversion is made, and their sum is not a cumulative simple return until it is transformed back. A zero or negative price makes the price ratio unsuitable for this calculation; stop and inspect the data rather than suppressing the resulting error.

Checkpoint: convert a simple return of 3% to a log return and transform it back. Preserve enough digits to verify the round trip.

## Issue 11 — How do simple and log returns reconcile?

The two return definitions satisfy

$$
g_t=\ln(1+r_t), \qquad r_t=\exp(g_t)-1.
$$

In Python, `np.log1p(r_t)` and `np.expm1(g_t)` reduce avoidable loss of precision for values close to zero. A valid simple return from a positive price sequence must be greater than -1. A reported simple return of -1.2 indicates a calculation, unit, or data problem under this setup.

For every nonmissing row, preserve an automated check that `simple_return` equals `np.expm1(log_return)` within numerical tolerance. If it fails, inspect price order, index alignment, and missing-value handling before changing the tolerance.

Checkpoint: explain why a simple return of exactly -100% has no finite log-return counterpart under this formula.

## Issue 12 — Why are multi-period simple returns compounded?

For consecutive simple returns from periods 1 through $T$, cumulative simple return is

$$
R_{0,T}=\prod_{t=1}^{T}(1+r_t)-1.
$$

If one period gains 10% and the next loses 10%, the sum is zero, but the compounded result is

$$
(1.10)(0.90)-1=-0.01=-1\%.
$$

The loss applies to a different base after the gain. Adding simple returns is therefore not the general aggregation rule. If a compounded result differs from the direct endpoint price change, first confirm that both calculations use the same first and last observations and that no distribution or external cash flow is treated differently.

Checkpoint: predict and then calculate the sum and compounded result for returns of 20% and -20%. Explain the difference using the changing wealth base.

## Issue 13 — What does a wealth index show?

A wealth index begins with one unit and applies consecutive simple returns:

$$
W_0=1, \qquad W_t=W_{t-1}(1+r_t).
$$

For a consistently constructed price series with no separately added cash flows,

$$
W_T=\frac{P_T}{P_0}.
$$

The first missing return may be treated as a neutral starting step only inside the wealth-index construction. It remains missing in the return series itself. Preserve both series so the starting convention is visible. A decreasing wealth index is not an error when the corresponding return is negative.

Checkpoint: write one sentence distinguishing an unobserved first return from the chosen initial wealth value of 1.

## Issue 14 — What is the difference between arithmetic and geometric mean returns?

For $T$ observed simple returns, the arithmetic mean is

$$
\bar r=\frac{1}{T}\sum_{t=1}^{T}r_t.
$$

The constant per-period return that produces the same terminal wealth is the geometric mean

$$
r_G=\left[\prod_{t=1}^{T}(1+r_t)\right]^{1/T}-1.
$$

For returns of 10% and -10%, the arithmetic mean is 0%, while the geometric mean is approximately -0.5013%. Neither statistic alone describes risk or future performance. If the geometric mean does not reproduce terminal wealth when compounded for $T$ periods, inspect the number of nonmissing returns and the use of the $T$th root.

Checkpoint: state which mean reproduces terminal wealth and write the identity you would use to verify it.

## Issue 15 — Are dates parsed, unique, and sorted?

Sequential calculations require an explicit date or timestamp index. Parse dates with an expected format, reject unparseable values, verify uniqueness, and sort before calculating returns. Sorting after returns have already been calculated does not repair the original interval calculation.

A duplicate timestamp is not automatically safe to delete. It may represent a duplicate record, two venues, two instruments sharing an incomplete identifier, or multiple observations within a session. Preserve the conflicting rows and investigate their fields and source keys.

The first diagnostic output should include row count, index type, minimum and maximum timestamp, uniqueness, and monotonic order. If two assets use different timestamp conventions, do not align them by display date until Issue 18 is resolved.

Checkpoint: describe why calculating returns before sorting cannot be repaired merely by sorting the already-calculated return column.

## Issue 16 — How should missing, stale, invalid, and extreme values be treated?

Different symptoms require different evidence:

- a missing field is an unavailable value in an existing row;
- an absent date may be a non-trading day or a missing observation;
- a repeated price may be valid, stale, or incorrectly filled;
- a zero or negative equity price is invalid for the return formulas used here; and
- an extreme return may be a real event, corporate action, unit error, or bad record.

Do not automatically fill, delete, winsorize, or replace a value. First preserve the original row, identify the source convention, state how the proposed action changes returns, and rerun the audit after the decision. A data-cleaning action is part of the financial assumption record, not a cosmetic programming step.

Checkpoint: choose one repeated price and write two competing explanations, one valid and one indicating a data problem, plus the evidence needed to distinguish them.

## Issue 17 — Is a missing calendar date a missing trading observation?

Markets do not trade in every calendar interval. Weekends, holidays, suspensions, and venue-specific schedules can produce no row. A generic business-day sequence also does not reproduce every real exchange holiday or special closing day.

The artificial Week 2 dates use weekdays only, but that convention does not describe an actual exchange. For real data, compare observed sessions with the official calendar for the relevant market and period. If a Monday follows a Friday, the Monday daily return spans the change between those two observed session endpoints; it is not three separately observed daily returns.

Checkpoint: distinguish `no scheduled session`, `scheduled session but missing record`, and `row present but price missing`. These cases should not receive one automatic treatment.

## Issue 18 — Do timestamps and frequency labels describe the same interval?

A date label can represent a session, a period start, a period end, or a timestamp in a stated time zone. Mechanical alignment requires compatible labels before returns are combined. For example, `2026-01-05 16:00 America/New_York` and `2026-01-06 05:00 Asia/Taipei` can refer to the same instant, while two timezone-naive strings cannot establish that relationship.

Week 2 records timestamp meaning, time zone, and interval endpoints. Week 3 separately examines publication time and whether information was available for a historical decision. Do not use Week 2 timestamp conversion as evidence that a variable was already public.

When converting daily observations to weekly observations, define the weekly endpoint and choose an aggregation that matches each field. A typical OHLCV aggregation uses first open, maximum high, minimum low, last close, and summed volume under a consistent session definition. A weekly price return is then calculated between weekly price endpoints; it is not the arithmetic mean of daily simple returns.

Checkpoint: explain why converting two timestamps to one time zone establishes mechanical comparability but does not establish that both values were publicly available at that instant.

## Issue 19 — Do multiple assets share a common holding interval?

Portfolio components must refer to the same start and end boundaries. Joining two return columns by an ending date is insufficient if one return covers one session and the other covers several sessions. One safe Week 2 procedure is to align the price series on common endpoints first and then calculate returns from those aligned prices. This changes the represented holding intervals and must be documented.

Do not forward-fill a missing asset price merely to create a complete portfolio row. That operation imposes a price path that was not observed. If a portfolio return is missing, display each component's start date, end date, price, and return before deciding whether the row can be used.

Checkpoint: draw the start and end boundaries for two candidate component returns and reject the combination when any boundary differs.

## Issue 20 — What does a one-period fixed-weight portfolio return mean?

For $N$ assets over one common holding interval,

$$
r_{p,t}=\sum_{i=1}^{N}w_{i,t-1}r_{i,t},
$$

where $w_{i,t-1}$ is established before the interval and $r_{i,t}$ refers to that interval. For the classroom example, weights are finite, nonnegative, and sum to one. If Asset A returns 2%, Asset B returns -1%, and the fixed weights are 60% and 40%, then

$$
r_{p,t}=0.60(0.02)+0.40(-0.01)=0.008=0.8\%.
$$

This calculation does not select the weights, model rebalancing, deduct trading costs, or establish future performance. Those questions belong to later weeks. If the weighted return appears on the first price date, first check whether every component return was required rather than silently summed as zero.

Checkpoint: recalculate the numerical example with 50% in each asset and state exactly which assumption changed.

## Reproduce the worked example in Python

The following cells connect Issues 1–20 using one artificial dataset. They are a worked example, not one of the four independent practice issues.

### Cell 1 — Record the artificial source and construct the table

```python
import numpy as np
import pandas as pd

source_record = {
    "provider": "instructor-created artificial example",
    "dataset": "Week 2 worked example",
    "market": "artificial",
    "currency": "artificial currency units",
    "frequency": "artificial daily sessions",
    "location": "included in week2_main.md",
    "version": "repository version of week2_main.md",
    "retrieval_date": "not applicable to an embedded fixed table",
    "real_market_claim_permitted": False,
}

records = [
    ["2026-01-05", 100.0, 101.5,  99.5, 100.0, 100.0, 1_000_000, 50.0],
    ["2026-01-06", 101.0, 103.0, 100.5, 102.0, 102.0, 1_100_000, 49.0],
    ["2026-01-07", 102.0, 102.5, 100.0, 101.0, 101.0,   950_000, 50.0],
    ["2026-01-08", 101.0, 104.5, 100.8, 104.0, 104.0, 1_250_000, 51.0],
    ["2026-01-09", 104.0, 104.2, 102.0, 103.0, 103.0,   980_000, 50.0],
    ["2026-01-12", 104.0, 107.0, 103.5, 106.0, 106.0, 1_400_000, 52.0],
    ["2026-01-13", 106.0, 106.3, 104.0, 105.0, 105.0, 1_050_000, 51.0],
    ["2026-01-14", 105.0, 108.0, 104.8, 107.0, 107.0, 1_300_000, 53.0],
    ["2026-01-15", 107.0, 109.0, 106.5, 108.0, 108.0, 1_200_000, 52.0],
    ["2026-01-16", 108.0, 111.0, 107.5, 110.0, 110.0, 1_500_000, 54.0],
]

columns = [
    "Date", "Open", "High", "Low", "Close", "Analysis_Price_A",
    "Volume", "Analysis_Price_B"
]

prices = pd.DataFrame(records, columns=columns)
prices["Date"] = pd.to_datetime(
    prices["Date"], format="%Y-%m-%d", errors="raise"
)
prices = prices.set_index("Date").sort_index()

print(pd.Series(source_record))
print(prices)
print("Shape:", prices.shape)
```

Expected evidence shows a `(10, 7)` table, a `DatetimeIndex`, dates from January 5 through January 16, and an explicit `False` for real-market claims. If the shape differs, compare the number of values in each record with the column list before continuing.

### Cell 2 — Audit identity, dates, units, and fields

```python
ohlc = ["Open", "High", "Low", "Close"]
high_violation = prices["High"] < prices[["Open", "Close"]].max(axis=1)
low_violation = prices["Low"] > prices[["Open", "Close"]].min(axis=1)
range_violation = prices["Low"] > prices["High"]

audit = pd.Series(
    {
        "rows": len(prices),
        "date_is_unique": prices.index.is_unique,
        "date_is_sorted": prices.index.is_monotonic_increasing,
        "timezone_recorded": False,
        "missing_analysis_prices": int(
            prices[["Analysis_Price_A", "Analysis_Price_B"]].isna().sum().sum()
        ),
        "nonpositive_analysis_prices": int(
            (prices[["Analysis_Price_A", "Analysis_Price_B"]] <= 0).sum().sum()
        ),
        "nonpositive_ohlc_prices": int((prices[ohlc] <= 0).sum().sum()),
        "negative_volume": int((prices["Volume"] < 0).sum()),
        "weekend_rows": int((prices.index.dayofweek >= 5).sum()),
        "ohlc_violations": int(
            (high_violation | low_violation | range_violation).sum()
        ),
    }
)

print(audit)

assert prices.index.is_unique
assert prices.index.is_monotonic_increasing
assert prices[["Analysis_Price_A", "Analysis_Price_B"]].notna().all().all()
assert (prices[["Analysis_Price_A", "Analysis_Price_B"]] > 0).all().all()
assert (prices[ohlc] > 0).all().all()
assert (prices["Volume"] >= 0).all()
assert (prices.index.dayofweek < 5).all()
assert audit["ohlc_violations"] == 0
```

All numerical audit counts should be zero except `rows`, which is 10. `timezone_recorded` remains `False` because the artificial source uses session-date labels rather than timestamps. That value is a disclosed limitation, not a passed check. If an assertion fails, inspect the affected rows; do not delete the assertion.

### Cell 3 — Calculate and reconcile returns

```python
result = pd.DataFrame(index=prices.index)
result["simple_A"] = prices["Analysis_Price_A"].pct_change(fill_method=None)
result["log_A"] = np.log(
    prices["Analysis_Price_A"] / prices["Analysis_Price_A"].shift(1)
)
result["wealth_A"] = (1 + result["simple_A"].fillna(0)).cumprod()

nonmissing_simple = result["simple_A"].dropna()
arithmetic_mean = nonmissing_simple.mean()
geometric_mean = (1 + nonmissing_simple).prod() ** (1 / len(nonmissing_simple)) - 1

print(result.round(6))
print("Arithmetic mean:", round(arithmetic_mean, 8))
print("Geometric mean:", round(geometric_mean, 8))

assert pd.isna(result["simple_A"].iloc[0])
assert pd.isna(result["log_A"].iloc[0])
assert np.allclose(
    result["simple_A"].dropna(),
    np.expm1(result["log_A"].dropna()),
)
assert np.isclose(
    result["wealth_A"].iloc[-1],
    prices["Analysis_Price_A"].iloc[-1]
    / prices["Analysis_Price_A"].iloc[0],
)
assert np.isclose(
    (1 + geometric_mean) ** len(nonmissing_simple),
    result["wealth_A"].iloc[-1],
)
```

Expected evidence shows missing first simple and log returns, an initial wealth index of 1, a final wealth index of 1.10, and three passed identities. The arithmetic and geometric means are not required to match. If the final identity fails, first check the first and last prices and the number of nonmissing returns.

### Cell 4 — Convert daily fields to weekly fields

```python
weekly_ohlcv = prices.resample("W-FRI").agg(
    {
        "Open": "first",
        "High": "max",
        "Low": "min",
        "Close": "last",
        "Volume": "sum",
        "Analysis_Price_A": "last",
        "Analysis_Price_B": "last",
    }
)
weekly_returns = weekly_ohlcv[["Analysis_Price_A", "Analysis_Price_B"]].pct_change(
    fill_method=None
)

print(weekly_ohlcv)
print(weekly_returns.round(6))

assert len(weekly_ohlcv) == 2
assert weekly_ohlcv.index.tolist() == [
    pd.Timestamp("2026-01-09"), pd.Timestamp("2026-01-16")
]
assert np.isclose(weekly_ohlcv.loc["2026-01-09", "Volume"], 5_280_000)
assert np.isclose(weekly_ohlcv.loc["2026-01-16", "Volume"], 6_450_000)
```

The weekly endpoints are January 9 and January 16. The first weekly return is missing, and the second Asset A return equals `110 / 103 - 1`. If the weekly prices are averages rather than endpoints, inspect the field-specific aggregation dictionary.

### Cell 5 — Calculate same-interval portfolio returns

```python
aligned_prices = prices[["Analysis_Price_A", "Analysis_Price_B"]].dropna()
asset_returns = aligned_prices.pct_change(fill_method=None)
asset_returns.columns = ["Asset_A", "Asset_B"]

weights = pd.Series({"Asset_A": 0.60, "Asset_B": 0.40})
assert np.isfinite(weights).all()
assert (weights >= 0).all()
assert np.isclose(weights.sum(), 1.0)

portfolio_return = asset_returns.mul(weights, axis=1).sum(
    axis=1, min_count=len(weights)
)
portfolio_table = asset_returns.assign(Portfolio=portfolio_return)

print(portfolio_table.round(6))
assert pd.isna(portfolio_table["Portfolio"].iloc[0])
assert portfolio_table.iloc[1:].notna().all().all()
```

Select one nonmissing row and verify the portfolio return by hand. The two asset returns, portfolio weights, and portfolio result must share one ending date and one starting date. If the first portfolio row is zero instead of missing, inspect `min_count` and the component returns.

## Practice issue 1 — Select fields across corporate actions

Use two independent artificial cases.

Case A: one share is priced at 150 before a three-for-one split; immediately afterward, three shares are priced at 50 each. Case B: a share price changes from 80 to 79 while a cash dividend of 2 is credited during the interval under the simplified convention in Issue 7.

Before calculating, predict which raw price-return signs will differ from the changes in economic holding value. Then:

1. calculate the raw per-share price return for Case A and compare total position value;
2. calculate the price return and simplified holding-period return for Case B;
3. specify which provider fields or definitions would be required before repeating each calculation with real data; and
4. identify one way a dividend or split could be counted twice.

Preserve the prediction, formulas, substituted values, units, revised explanation, and provider evidence still needed. The work is complete when the share-count effect and dividend effect are explained separately. If they are combined into one unexplained adjusted field, return to Issues 5–7.

## Practice issue 2 — Reconcile simple, log, cumulative, and average returns

Use these artificial analysis prices:

| Date | Asset C analysis price |
|---|---:|
| 2026-02-02 | 100 |
| 2026-02-03 | 104 |
| 2026-02-04 | 101 |
| 2026-02-05 | 106 |
| 2026-02-06 | 103 |

Before using code, predict whether final wealth is above or below 1 and whether the arithmetic mean exceeds the geometric mean. Then independently:

1. calculate every simple and log return;
2. reconcile the two definitions with $r_t=\exp(g_t)-1$;
3. calculate cumulative simple return and the wealth index;
4. verify terminal wealth against the endpoint price ratio; and
5. calculate arithmetic and geometric mean returns and verify that the geometric mean reproduces terminal wealth.

Use new variable names rather than copying the worked-example objects. Expected evidence contains five prices, four nonmissing simple returns, four nonmissing log returns, a wealth index beginning at 1, and three passed identities. If the endpoint check fails, inspect date order and compounding before changing a formula.

## Practice issue 3 — Audit a damaged financial table

Create this table without repairing it:

```python
damaged_records = [
    ["2026-03-02 16:00", 50.0, 2_000],
    ["2026-03-03 16:00", 51.0, 2_100],
    ["2026-03-03 16:00", 51.2, 2_100],
    ["2026-03-05 16:00", np.nan, 1_900],
    ["2026-03-04 16:00", 0.0, 2_200],
]
```

The columns are `Timestamp`, `Analysis_Price`, and `Volume`. The source says only that timestamps are local exchange time; it does not name the exchange or time zone.

Before coding the audit, predict which checks fail. Then produce evidence for row count, timestamp parsing, sorting, duplicate timestamps, missing prices, nonpositive prices, and missing time-zone metadata. For every failed check, state:

```text
Why the problem can change a return:
Evidence required before repair:
Proposed action or pending decision:
Effect of the action on the retained sample:
```

Do not calculate returns from the damaged table and do not silently select one duplicate row. Expected evidence identifies five rows, one duplicated timestamp beyond its first occurrence, one missing price, one nonpositive price, unsorted input, and unresolved time-zone metadata. If the audit reports a clean table, display the parsed rows before writing any repair code.

## Practice issue 4 — Align assets before calculating a portfolio return

Use these artificial prices:

| Date | Asset D price | Asset E price |
|---|---:|---:|
| 2026-03-02 | 80 | 40 |
| 2026-03-03 | 82 | 39 |
| 2026-03-04 | 81 | missing |
| 2026-03-05 | 84 | 42 |
| 2026-03-06 | 83 | 43 |

Before coding, predict which displayed dates can be endpoints for a portfolio return that uses both assets. Do not forward-fill Asset E. Then:

1. preserve the original table and its missing-value audit;
2. create a common price table using dates on which both prices are observed;
3. calculate returns only after creating that common price table;
4. label the start and end of every resulting holding interval;
5. calculate the return ending March 5 for fixed weights of 70% Asset D and 30% Asset E; and
6. explain why Asset D's March 4 daily return cannot be combined with Asset E's March 3-to-March 5 return.

Expected evidence contains the original five rows, four common price endpoints, three common holding intervals, finite weights that sum to one, and no portfolio result on the first common price date. If the March 5 components span different start dates, return to the common price table and recalculate both returns.

## Required evidence and completion

Submit one notebook or exported report containing:

1. a completed data-processing record from [Week 2 Support](week2_support.md#data-processing-record);
2. the five worked-example cells and their visible audit outputs;
3. one checkpoint response for each of Issues 1–20;
4. predictions, calculations, revisions, and preserved evidence for Practice issues 1–4;
5. a table listing unresolved provider, field, calendar, currency, or time-zone questions; and
6. one conclusion supported by the artificial evidence and one conclusion it cannot support.

The Week 2 work is complete when another student can identify every input and unit, rerun the notebook from a clean environment, reproduce every return identity, distinguish calendar gaps from data problems, identify common holding intervals, and explain why the evidence is not a trading-performance result.

The source record, field definitions, return calculations, date checks, and alignment evidence begin the data documentation required by the [Report 1 criteria](../10/week10_main.md). Week 2 does not change the report weights or add another grading criterion.

## Exit evidence

Complete these statements:

```text
The price or return field I used represents ______.
The most consequential data problem I checked was ______ because ______.
My multi-asset returns share the interval from ______ to ______.
The Week 2 evidence supports the conclusion that ______.
The Week 2 evidence does not support the conclusion that ______.
Before using real data, I still need provider evidence for ______.
```

A correct historical return describes the selected data, interval, and assumptions. It does not establish predictability, profitability after costs, or future investment performance.
