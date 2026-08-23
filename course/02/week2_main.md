# Week 2 — Financial Data and Returns

## Observable outcome

By the end of this two-hour class, you will produce an audited return table from a clearly identified price series. The table will contain simple returns, log returns, a cumulative wealth index, weekly returns, and one-period portfolio returns. You will also submit a short record of the data-processing decisions that make those results interpretable and reproducible.

The numerical data used in this class are artificial and are provided only for calculation practice. They do not describe a real security and must not be used to make an investment claim.

## Teaching Objectives

After completing this class, you should be able to:

- distinguish price levels from returns and explain the roles of Open, High, Low, Close, adjusted price, and Volume;
- calculate and interpret simple, log, cumulative, and one-period portfolio returns;
- verify dates, ordering, duplicates, missing values, price validity, and data frequency before using a return series;
- reproduce the calculations in Python and preserve evidence of the inputs, assumptions, checks, and outputs; and
- state what the Week 2 evidence supports and what it does not support.

## Before you begin

You need a browser-based or local Python notebook with `numpy` and `pandas`. Run the cells in this document from top to bottom. If your environment is not ready, use the preparation instructions in [Week 2 Support](week2_support.md#before-class).

Use the artificial data in this document for all required work. Do not replace it with an online download unless the instructor provides a data source, field definition, time range, and permission for classroom use.

## Two-hour learning sequence

| Time | Focus | Evidence produced |
|---:|---|---|
| 0–10 minutes | Price or return? | One sentence identifying the evidence needed to compare two investments |
| 10–25 minutes | Price fields and adjusted prices | A field-selection decision with a stated reason |
| 25–45 minutes | Simple and log returns | Hand calculations and an interpretation in percentage terms |
| 45–60 minutes | Compounding and cumulative return | A comparison of arithmetic addition and compounding |
| 60–75 minutes | Python reproduction | An audited return table and two automated checks |
| 75–90 minutes | Dates, duplicates, missing values, and trading days | A three-row data-quality decision table |
| 90–105 minutes | Daily and weekly frequency | A comparison using the same underlying price path |
| 105–115 minutes | One-period portfolio return | A fixed-weight portfolio-return calculation with its assumption stated |
| 115–120 minutes | Exit evidence | One valid conclusion, one unsupported conclusion, and one unresolved question |

## 1. A price is not an investment result

A price level tells you the value assigned to an asset at one time. A return measures the change in value relative to an earlier value. A price of 200 is not automatically better than a price of 100, because the two assets may have different starting prices, units, corporate actions, and histories.

Daily market data commonly include Open, High, Low, Close, and Volume. An adjusted price is a provider-defined series intended to make observations across certain corporate actions more comparable. The exact adjustments vary by provider, so you must read the provider's definition and record whether dividends, splits, or other events are included. Never assume that every column named `Adjusted Close` has the same construction.

The full field descriptions are in the [data dictionary](week2_support.md#data-dictionary).

### Activity 1 — Select the calculation field

Individually, decide which field you would use to calculate a multi-period holding return: `Open`, `Close`, or a provider-defined adjusted price. Write one reason and one condition that must be checked before your choice is valid. Compare your answer with another student and revise it if the condition was missing.

Your visible output is two sentences:

```text
I would use ______ because ______.
This choice is valid only if ______.
```

## 2. Simple and log returns

Let \(P_t\) be the selected price for period \(t\), measured in the same currency and constructed consistently across dates.

The simple return is

\[
r_t = \frac{P_t}{P_{t-1}} - 1.
\]

The log return is

\[
g_t = \ln\left(\frac{P_t}{P_{t-1}}\right) = \ln(1+r_t).
\]

Both are decimal returns. Multiply by 100 only when reporting percentage points. For example, if the price rises from 100 to 102,

\[
r_t = 0.02 = 2\%, \qquad g_t = \ln(1.02) \approx 0.019803.
\]

A simple return describes the proportional gain or loss over one period. Log returns are additive across consecutive periods, but a summed log return must be converted back with \(\exp(\sum g_t)-1\) when a cumulative simple return is required.

### Activity 2 — Calculate before coding

Use these artificial adjusted prices for Asset A:

| Date | Adjusted price |
|---|---:|
| 2026-01-05 | 100 |
| 2026-01-06 | 102 |
| 2026-01-07 | 101 |
| 2026-01-08 | 104 |

Calculate the simple and log returns for January 7 and January 8. Keep at least six decimal places during calculation and report percentages only at the end. Then answer: which return definition can be added across the two days without an additional compounding step?

Your result is complete when both formulas, substituted prices, decimal results, and one interpretation sentence are visible.

## 3. Cumulative return and the wealth index

For consecutive simple returns from period 1 through \(T\), the cumulative simple return is

\[
R_{0,T} = \prod_{t=1}^{T}(1+r_t)-1.
\]

If the same consistently adjusted price series is used and there are no external cash flows in the calculation,

\[
R_{0,T} = \frac{P_T}{P_0}-1.
\]

A wealth index starts at 1 and shows the value of one unit invested through the return sequence:

\[
W_0=1, \qquad W_t=W_{t-1}(1+r_t).
\]

Adding simple returns is generally not the same as compounding them. The difference becomes important when returns are large, volatile, or measured over many periods.

### Activity 3 — Find the aggregation error

Using the four prices in Activity 2, compare these three calculations:

1. the sum of the three simple returns;
2. the compounded cumulative simple return; and
3. the change from the first price directly to the last price.

Identify which two must agree, allowing for rounding, and explain why the remaining calculation can differ.

## 4. Reproduce the calculations in Python

Create a new notebook and run the following cells in order. This is a complete artificial dataset for Week 2; no download is required.

### Cell 1 — Create and index the data

```python
import numpy as np
import pandas as pd

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
    "Date", "Open", "High", "Low", "Close", "Adj_Close", "Volume",
    "Asset_B_Adj_Close"
]

prices = pd.DataFrame(records, columns=columns)
prices["Date"] = pd.to_datetime(
    prices["Date"], format="%Y-%m-%d", errors="raise"
)
prices = prices.set_index("Date").sort_index()

print(prices)
print("Shape:", prices.shape)
print("Index type:", type(prices.index).__name__)
```

Expected evidence:

- the shape is `(10, 7)`;
- the index type is `DatetimeIndex`;
- dates run from 2026-01-05 through 2026-01-16 in ascending order; and
- no row represents a weekend.

If your shape is different, first check the number of values in each record and the column list. If the index is not `DatetimeIndex`, inspect the output of `prices.dtypes` before continuing.

### Cell 2 — Audit the price table

```python
high_violation = prices["High"] < prices[["Open", "Close"]].max(axis=1)
low_violation = prices["Low"] > prices[["Open", "Close"]].min(axis=1)
range_violation = prices["Low"] > prices["High"]

audit = pd.Series(
    {
        "rows": len(prices),
        "date_is_unique": prices.index.is_unique,
        "date_is_sorted": prices.index.is_monotonic_increasing,
        "missing_adj_close": int(prices["Adj_Close"].isna().sum()),
        "nonpositive_adj_close": int((prices["Adj_Close"] <= 0).sum()),
        "ohlc_violations": int(
            (high_violation | low_violation | range_violation).sum()
        ),
    }
)

print(audit)

assert prices.index.is_unique
assert prices.index.is_monotonic_increasing
assert prices["Adj_Close"].notna().all()
assert (prices["Adj_Close"] > 0).all()
assert audit["ohlc_violations"] == 0
```

The cell is complete when every assertion runs without an error and the audit contains zero missing adjusted prices, zero nonpositive adjusted prices, and zero OHLC violations. These checks establish basic internal consistency; they do not prove that a real data provider reported the values correctly.

### Cell 3 — Calculate returns and verify identities

```python
result = pd.DataFrame(index=prices.index)
result["simple_A"] = prices["Adj_Close"].pct_change(fill_method=None)
result["log_A"] = np.log(
    prices["Adj_Close"] / prices["Adj_Close"].shift(1)
)
result["wealth_A"] = (1 + result["simple_A"].fillna(0)).cumprod()

print(result.round(6))

assert np.allclose(
    result["simple_A"].dropna(),
    np.expm1(result["log_A"].dropna()),
)
assert np.isclose(
    result["wealth_A"].iloc[-1] - 1,
    prices["Adj_Close"].iloc[-1] / prices["Adj_Close"].iloc[0] - 1,
)
```

Expected evidence:

- the first simple and log returns are `NaN` because no earlier price is present;
- the wealth index begins at 1;
- the final wealth index is 1.10; and
- both assertions run without an error.

Do not replace the first missing return with an invented zero when estimating return statistics. The zero is used only inside the wealth-index calculation to establish the starting value.

## 5. Audit dates, duplicates, and missing values

A missing calendar date is not automatically a missing trading observation. Weekends and market holidays normally have no trading row. In contrast, a missing price inside an observed trading row, a duplicated timestamp, or unsorted dates can invalidate return calculations.

Run this cell to create an intentionally damaged copy of the artificial data:

```python
damaged = prices.reset_index().copy()
damaged.loc[3, "Adj_Close"] = np.nan
damaged = pd.concat([damaged, damaged.iloc[[5]]], ignore_index=True)
damaged = damaged.sample(frac=1, random_state=7).reset_index(drop=True)

damage_report = pd.Series(
    {
        "missing_adj_close": int(damaged["Adj_Close"].isna().sum()),
        "duplicated_dates": int(damaged["Date"].duplicated().sum()),
        "date_is_sorted": damaged["Date"].is_monotonic_increasing,
    }
)
print(damage_report)
```

The report should show one missing adjusted price, one duplicated date, and unsorted dates. Do not repair the table immediately. First complete this decision table:

| Problem | Why it can change returns | Evidence needed before repair | Proposed action |
|---|---|---|---|
| Missing adjusted price |  |  |  |
| Duplicated date |  |  |  |
| Unsorted dates |  |  |  |

Sorting dates is necessary before calculating sequential returns. A duplicate should be investigated before deletion. A missing price should not be forward-filled merely to remove `NaN`; doing so imposes a zero return and may create evidence that the source never reported.

## 6. Change the data frequency correctly

Changing from daily to weekly frequency changes the time represented by each return. To obtain a weekly price series, select the last observed adjusted price in each weekly interval and then calculate returns from those weekly prices.

```python
weekly_prices = prices[["Adj_Close", "Asset_B_Adj_Close"]].resample(
    "W-FRI"
).last()
weekly_returns = weekly_prices.pct_change(fill_method=None)

print("Weekly prices")
print(weekly_prices)
print("\nWeekly returns")
print(weekly_returns.round(6))
```

Expected evidence:

- the weekly price table has two rows, ending on January 9 and January 16;
- the first weekly return is missing because an earlier weekly price is absent; and
- the second weekly return compounds all daily changes between the two weekly endpoints.

### Activity 4 — Daily versus weekly evidence

Compare the second weekly return for Asset A with the compounded daily returns over the same endpoints. They should agree within numerical precision. Explain why summing the daily simple returns is not the required aggregation rule.

Then write one reason why a model evaluated with daily returns and the same model evaluated with weekly returns are answering different questions.

## 7. Calculate a one-period portfolio return

For \(N\) assets, the one-period portfolio return is

\[
r_{p,t}=\sum_{i=1}^{N} w_{i,t-1}r_{i,t},
\]

where \(w_{i,t-1}\) is the portfolio weight established before period \(t\) begins. The weights and asset returns must refer to the same holding interval.

For this arithmetic exercise only, assume the portfolio is reset before every daily interval to 60% Asset A and 40% Asset B. This assumption is explicit; it is not a recommended weighting method. Later weeks will examine how weights are selected and when portfolios are rebalanced.

```python
asset_returns = prices[["Adj_Close", "Asset_B_Adj_Close"]].pct_change(
    fill_method=None
)
asset_returns.columns = ["Asset_A", "Asset_B"]

weights = pd.Series({"Asset_A": 0.60, "Asset_B": 0.40})
assert np.isclose(weights.sum(), 1.0)

portfolio_return = asset_returns.mul(weights, axis=1).sum(
    axis=1, min_count=len(weights)
)

portfolio_table = asset_returns.assign(Portfolio=portfolio_return)
print(portfolio_table.round(6))
```

The first portfolio return must remain missing because both asset returns are missing. Select one later date and verify the Python result by substituting both asset returns and weights into the formula by hand.

## 8. Create the required evidence

Submit one notebook or exported report containing:

1. the hand calculations from Activities 2 and 3;
2. the complete artificial input table and its audit output;
3. the daily return table with `simple_A`, `log_A`, and `wealth_A`;
4. the completed three-row data-quality decision table;
5. the daily-versus-weekly comparison from Activity 4;
6. one hand-verified portfolio return; and
7. a data-processing record using the template in [Week 2 Support](week2_support.md#data-processing-record).

Your work is complete when another student can identify the input prices, reproduce the calculations from a clean notebook, see which checks passed, and understand every assumption without asking what you meant.

## Exit evidence

Complete these three statements before leaving:

```text
The Week 2 evidence supports the conclusion that ______.
The Week 2 evidence does not support the conclusion that ______.
Before using real market data, I still need to verify ______.
```

A correctly calculated historical return describes the selected data and period. It does not establish predictability, profitability after costs, or future investment performance.
