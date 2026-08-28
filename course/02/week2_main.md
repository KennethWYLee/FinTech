# Week 2 — Financial Data and Returns

## Observable outcome

After completing Week 2, you will produce an audited return table from a clearly identified price series. The table will contain simple returns, log returns, a cumulative wealth index, weekly returns, and one-period portfolio returns. You will also submit a short record of the data-processing decisions that make those results interpretable and reproducible.

The numerical data used in this class are artificial and are provided only for calculation practice. They do not describe a real security and must not be used to make an investment claim.

## Teaching Objectives

After completing this class, you should be able to:

- distinguish price levels from returns, explain the roles of Open, High, Low, Close, adjusted price, and Volume, and state why corporate-action treatment must be verified;
- calculate and interpret simple, log, cumulative, and one-period portfolio returns;
- verify dates, ordering, duplicates, missing values, price validity, data frequency, and common holding intervals before combining return series;
- reproduce the calculations in Python and preserve evidence of the inputs, assumptions, checks, and outputs; and
- state what the Week 2 evidence supports and what it does not support.

## Before you begin

You need a browser-based or local Python notebook with `numpy` and `pandas`. Run the cells in this document from top to bottom. If your environment is not ready, use the preparation instructions in [Week 2 Support](week2_support.md#before-class).

Use the artificial data in this document for all required work. Do not replace it with an online download unless the instructor provides a data source, field definition, time range, and permission for classroom use.

## 1. A price is not an investment result

Before reading the definitions below, compare two artificial investments over the same interval: one price rises from 20 to 25, while another rises from 200 to 210. Predict which performed better and write one sentence identifying the evidence needed for a valid comparison. The expected comparison uses relative changes over the same interval rather than the ending prices or currency-unit changes alone. If your conclusion differs, first check the starting value used as the denominator for each investment.

A price level tells you the value assigned to an asset at one time. A return measures the change in value relative to an earlier value. A price of 200 is not automatically better than a price of 100, because the two assets may have different starting prices, units, corporate actions, and histories.

Daily market data commonly include Open, High, Low, Close, and Volume. Open is the first recorded transaction price for the session under the dataset's convention; High and Low are the largest and smallest recorded session prices; Close is the final recorded transaction price; and Volume records the reported quantity traded. Exact session and field definitions still depend on the provider and market.

An adjusted price is a provider-defined series intended to make observations across certain corporate actions more comparable. The exact adjustments vary by provider, so you must read the provider's definition and record whether dividends, splits, or other events are included. Never assume that every column named `Adjusted Close` has the same construction.

The full field descriptions are in the [data dictionary](week2_support.md#data-dictionary).

### Worked example — a split changes the unit count

Suppose an investor owns one artificial share priced at 100 immediately before a two-for-one split. Ignoring market movement, taxes, fees, and rounding, the investor owns two shares priced at 50 immediately afterward. The position value is 100 before and after the split, but the unadjusted one-share price calculation is

$$
\frac{50}{100}-1=-0.50.
$$

The negative 50% calculation reflects the change in units and is not the investor's economic holding return in this example. A data provider may supply an adjusted or total-return series to address specified corporate actions, but the provider's documentation must determine what was adjusted and how. The label alone is insufficient evidence.

### Activity 1 — Select the calculation field

Individually, decide which field you would use to calculate a multi-period holding return: `Open`, `Close`, or a provider-defined adjusted price. Write one reason and one condition that must be checked before your choice is valid. Then consider a different artificial case: one share is priced at 120 before a three-for-one split and three shares are priced at 40 afterward. Calculate the raw one-share price return, compare the total position value before and after the split, and identify the provider evidence needed before using an adjusted field. Compare your answer with another student and revise it if the condition was missing.

Your visible output includes the calculation and these two sentences:

```text
I would use ______ because ______.
This choice is valid only if ______.
```

Expected evidence shows that the raw per-share return and the change in total position value answer different questions in the split example. If they appear to describe the same change, first check whether the number of shares was held constant in one calculation but changed in the other.

## 2. Simple and log returns

Let $P_t$ be the selected price for period $t$, measured in the same currency and constructed consistently across dates.

The simple return is

$$
r_t = \frac{P_t}{P_{t-1}} - 1.
$$

The log return is

$$
g_t = \ln\left(\frac{P_t}{P_{t-1}}\right) = \ln(1+r_t).
$$

Both are decimal returns. Multiply by 100 when reporting a return in percent. A percentage point instead describes the arithmetic difference between two percentages. For example, if the price rises from 100 to 102,

$$
r_t = 0.02 = 2\%, \qquad g_t = \ln(1.02) \approx 0.019803.
$$

A simple return describes the proportional gain or loss over one period. Log returns are additive across consecutive periods, but a summed log return must be converted back with $\exp(\sum g_t)-1$ when a cumulative simple return is required.

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

The expected signs follow the direction of each price change, and each simple return must agree with `expm1` applied to the corresponding log return within rounding. If the identity fails, first check the order of $P_t$ and $P_{t-1}$ and confirm that the natural logarithm was used.

## 3. Cumulative return and the wealth index

For consecutive simple returns from period 1 through $T$, the cumulative simple return is

$$
R_{0,T} = \prod_{t=1}^{T}(1+r_t)-1.
$$

If the same consistently adjusted price series is used and there are no external cash flows in the calculation,

$$
R_{0,T} = \frac{P_T}{P_0}-1.
$$

A wealth index starts at 1 and shows the value of one unit invested through the return sequence:

$$
W_0=1, \qquad W_t=W_{t-1}(1+r_t).
$$

Adding simple returns is generally not the same as compounding them. The difference becomes important when returns are large, volatile, or measured over many periods.

### Activity 3 — Find the aggregation error

Using the four prices in Activity 2, compare these three calculations:

1. the sum of the three simple returns;
2. the compounded cumulative simple return; and
3. the change from the first price directly to the last price.

Identify which two must agree, allowing for rounding, and explain why the remaining calculation can differ.

The compounded return and the direct endpoint change must agree. If they do not, first verify that every one-period return includes the `+1` term before multiplication and that the same first and last prices define both calculations.

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
ohlc_columns = ["Open", "High", "Low", "Close"]

audit = pd.Series(
    {
        "rows": len(prices),
        "date_is_unique": prices.index.is_unique,
        "date_is_sorted": prices.index.is_monotonic_increasing,
        "missing_adj_close": int(prices["Adj_Close"].isna().sum()),
        "nonpositive_adj_close": int((prices["Adj_Close"] <= 0).sum()),
        "missing_asset_b_price": int(prices["Asset_B_Adj_Close"].isna().sum()),
        "nonpositive_asset_b_price": int((prices["Asset_B_Adj_Close"] <= 0).sum()),
        "nonpositive_ohlc_prices": int((prices[ohlc_columns] <= 0).sum().sum()),
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
assert prices["Adj_Close"].notna().all()
assert (prices["Adj_Close"] > 0).all()
assert prices["Asset_B_Adj_Close"].notna().all()
assert (prices["Asset_B_Adj_Close"] > 0).all()
assert (prices[ohlc_columns] > 0).all().all()
assert (prices["Volume"] >= 0).all()
assert (prices.index.dayofweek < 5).all()
assert audit["ohlc_violations"] == 0
```

The cell is complete when every assertion runs without an error and the audit contains zero missing or nonpositive analysis prices, zero nonpositive OHLC prices, zero negative volumes, zero weekend rows, and zero OHLC violations. These checks establish basic internal consistency; they do not prove that a real data provider reported the values correctly or that every weekday was an actual trading session.

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

The expected numerical relationship is equality, within floating-point precision, between the weekly endpoint return and the compounded daily returns from the same endpoints. If they differ, first list the included dates and confirm that the daily sequence begins after the earlier Friday endpoint and ends on the later Friday endpoint.

## 7. Calculate a one-period portfolio return

For $N$ assets, the one-period portfolio return is

$$
r_{p,t}=\sum_{i=1}^{N} w_{i,t-1}r_{i,t},
$$

where $w_{i,t-1}$ is the portfolio weight established before period $t$ begins. The weights and asset returns must refer to the same holding interval.

For example, an Asset A return measured from the January 6 close to the January 7 close cannot be combined with an Asset B return measured from the January 7 close to the January 8 close. Even if both values are individually correct, their weighted sum does not describe one portfolio holding interval. Before multiplication, verify that the asset-return columns share the same date label and interval definition.

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

The hand calculation and Python value must agree within rounding, and both component returns must have the selected date label. If they do not, first inspect the weights, column labels, and date of each component before changing the formula.

## 8. Complete an independent return audit

Use the following second artificial dataset. It is deliberately different from the worked example and contains only the fields needed for this exercise.

| Date | Asset C price | Asset D price |
|---|---:|---:|
| 2026-02-02 | 80 | 40 |
| 2026-02-03 | 82 | 39 |
| 2026-02-04 | 81 | 41 |
| 2026-02-05 | 84 | 42 |
| 2026-02-06 | 83 | 43 |

Before calculating, predict whether the final wealth index of each asset will be above or below 1. Record the prediction without using code. Then complete these tasks independently:

1. parse and sort the dates and verify uniqueness, missing values, and positive prices;
2. calculate simple and log returns for both assets without filling the first missing return;
3. construct a wealth index for each asset and verify each final value against its final-price-to-initial-price ratio;
4. calculate the February 4 return of a portfolio reset before that interval to 70% Asset C and 30% Asset D; and
5. revise the initial prediction and write one conclusion supported by this artificial calculation.

Expected evidence consists of five price rows, four nonmissing returns per asset, two endpoint-identity checks that pass within numerical precision, and one portfolio return based on two returns from the same holding interval. If a final wealth identity fails, first inspect date order, the first return, and whether simple returns were compounded instead of added. If the portfolio return is missing or uses mismatched dates, first display the two component returns and their index labels.

Save the prediction, audited input, calculated table, identity checks, hand substitution for the portfolio return, and revised conclusion. This exercise is complete only when another student can reproduce it without reusing the code variables from the earlier example.

## 9. Create the required evidence

Submit one notebook or exported report containing:

1. the initial price comparison, field decision, and corporate-action calculation from Section 1 and Activity 1;
2. the hand calculations from Activities 2 and 3;
3. the complete artificial input table and its audit output;
4. the daily return table with `simple_A`, `log_A`, and `wealth_A`;
5. the completed three-row data-quality decision table;
6. the daily-versus-weekly comparison from Activity 4;
7. one hand-verified portfolio return;
8. the independent return-audit evidence from Section 8; and
9. a data-processing record using the template in [Week 2 Support](week2_support.md#data-processing-record).

Your work is complete when another student can identify the input prices, reproduce the calculations from a clean notebook, see which checks passed, and understand every assumption without asking what you meant.

The data definition, return calculations, date checks, and source record provide the data evidence required by the [Report 1 criteria](../10/week10_main.md). This lesson supplies evidence for those criteria; it does not change the report weights or add another criterion.

## Exit evidence

Complete these three statements before leaving:

```text
The Week 2 evidence supports the conclusion that ______.
The Week 2 evidence does not support the conclusion that ______.
Before using real market data, I still need to verify ______.
```

A correctly calculated historical return describes the selected data and period. It does not establish predictability, profitability after costs, or future investment performance.
