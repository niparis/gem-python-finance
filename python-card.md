# Python card

One page, one section per session. Keep it open while you work. It grows every week.

## Session 1: first Python, through finance

| Python name | You write | It means | Finance use |
|---|---|---|---|
| **Variable** | `rate = 0.07` | a named value | an assumption |
| **Operator `**`** | `(1 + rate) ** years` | "to the power of" | compounding, discounting |
| **f-string** | `f"PV = {pv:,.2f}"` | text with a number inside | a readable output |
| **Function** | `def present_value(...):`<br>`    return ...` | a reusable formula with inputs and a result | PV, FV, NPV |
| **List + index** | `cash_flows[0]` | values in order; positions start at 0 | year 0 = position 0 |
| **For loop** | `for t, cf in enumerate(cash_flows):` | repeat for every item, with its position | discount each cash flow |
| **Condition** | `if npv > 0: ... else: ...` | decide | invest or not |
| **Dictionary** | `{"d0": 0.27, "r": 0.12}` | values looked up by name (a key) | the assumptions tab |

### Functions that work together

One **function** per step, chained into a value. Each step can be checked alone, like one Excel block per calculation.

```
dividends()  ->  terminal_value()  ->  discount()  ->  assessment()  ->  "overvalued"
```

| Python name | You write | It means |
|---|---|---|
| **Condition** (three outcomes) | `if ...: ... elif ...: ... else: ...` | undervalued, fair or overvalued |
| **Guard** | `if g >= r: raise ValueError("...")` | a condition that stops the code with your own message |
| **`enumerate` from 1** | `enumerate(values, start=1)` | count from 1: use it when the first value is year 1 |

### Error types you can now read

Read the last line first, then find the arrow `---->`, then ask: which value is not what I think?

| Error type | Meaning | Example |
|---|---|---|
| `TypeError` | wrong kind of value | `1 + "7%"`: a number and text |
| `ZeroDivisionError` | divided by 0 | `r - g` when g = r |
| `ValueError` | a value that makes no sense (here: raised by your own guard) | g >= r in Gordon growth |

## Session 2: NumPy, through portfolio risk

| Python name | You write | It means | Finance use |
|---|---|---|---|
| **Import** | `import numpy as np` | load a toolbox, call it `np` | arrays and maths |
| **Array** | `np.array([0.2, 0.3, 0.5])` | numbers you compute with at once | weights, vols, returns |
| **Vectorized operation** | `w * ret` | value by value, no loop | each fund's contribution |
| **Sum** | `np.sum(w * ret)` | add all values | portfolio return |
| **Matrix product** | `w @ cov @ w` | rows times columns | portfolio variance |
| **2D array + index** | `corr[0, 2]` | row, column (from 0) | correlation of A and C |
| **Diagonal matrix** | `np.diag(vol)` | vols on the diagonal, 0 elsewhere | build the covariance |
| **Square root** | `np.sqrt(var)` | variance to volatility | portfolio volatility |
| **Random generator + seed** | `np.random.default_rng(42)` | random numbers, the same every run | simulations |
| **Shape** | `size=(252, 5000)` | 252 rows, 5,000 columns | days x paths |

### More NumPy used in the notebooks

| Python name | You write | It means | Finance use |
|---|---|---|---|
| **Product down a column** | `np.prod(1 + daily, axis=0)` | multiply the rows of each column | value after a year, one per path |
| **Running product** | `np.cumprod(1 + daily, axis=0)` | multiply as you go down | value of the fund day after day |
| **Median, percentile** | `np.median(final)`, `np.percentile(final, 5)` | the middle value; the value 5% of paths end below | typical year, bad year |
| **Comparison + mean** | `(final < 100).mean()` | True/False for each path; the mean is the share of True | chance of a loss |
| **Ones, full, fill diagonal** | `np.ones((3, 3))`, `np.full((3, 3), 0.9)`, `np.fill_diagonal(m, 1.0)` | arrays built from one value | known case, crisis correlations |
| **Round** | `np.round(cov, 4)` | show 4 decimals | a readable matrix |

### Functions on arrays

One formula, any portfolio. The guard stops the code if the weights do not sum to 1.

```
def portfolio_vol(weights, vol, corr):
    if round(np.sum(weights), 6) != 1:
        raise ValueError("Weights must sum to 1.")
    cov = np.diag(vol) @ corr @ np.diag(vol)
    return np.sqrt(weights @ cov @ weights)
```

| Python name | You write | It means | Finance use |
|---|---|---|---|
| **Function** | `def portfolio_vol(weights, vol, corr):` | one formula, any portfolio | return and risk of any weights |
| **Guard** | `if ...: raise ValueError(...)` | stop if the weights do not sum to 1 | no result is better than a wrong one |
| **round** | `round(np.sum(weights), 6)` | `0.7 + 0.2 + 0.1` gives 0.9999999999999999: never test decimals with `==` | the weights test |
| **Known case** | `corr = np.ones((3, 3))` | a check you can always run | all correlations 1 gives the weighted average |

## Session 3: pandas, through real prices and a PDF

| Python name | You write | It means | Finance use |
|---|---|---|---|
| **Import** | `import pandas as pd` | load the table toolbox, call it `pd` | prices, returns |
| **DataFrame** | `pd.read_excel("peers.xlsx", index_col="Date")` | a table: index, columns, rows | five years of prices |
| **Index** | `prices.index` | the labels of the rows (here, dates) | one row per trading day |
| **Column** | `prices["AAPL"]` | one column, by name | one stock |
| **Row by label** | `prices.loc["2026-11-13"]` | one row, by its date | one day, all stocks |
| **Row by position** | `prices.iloc[0]`, `prices.iloc[-1]` | one row, by position (from 0; -1 is the last) | the first and the last day |
| **Method** | `prices.pct_change().dropna()` | an action on the table, chained one after the other | daily returns |
| **Statistic per column** | `returns.std()`, `returns.corr()`, `returns.cov()` | one result per stock | volatility, correlation, covariance |
| **Resample** | `prices.resample("YE").last()` | one row per year | yearly returns |
| **Rolling window** | `returns.rolling(63).std()` | the last 63 rows, moving one day at a time | risk over time |
| **Chart** | `rebased.plot()` | one line per column | the backtest chart |
| **Export** | `vol.to_excel("vol.xlsx")` | write an Excel file | send it to the MD |
| **DataFrame from columns** | `pd.DataFrame({"Volatility": vol, "Beta": beta})` | one column per key of the dictionary, one row per stock | the risk table |
| **Sort** | `table.sort_values("Sharpe", ascending=False)` | best first | rank by risk-adjusted return |
| **Round** | `table.round(3)` | a shorter copy of the table (0.196 is 19.6%) | a readable table |
| **Dates, day first** | `pd.read_csv("file.csv", parse_dates=["Date"], dayfirst=True)` | read `01/02/2024` as 1 February | European dates |

### Text methods: reading a PDF

A PDF gives text, line by line. These tools turn it into numbers.

| Python name | You write | It means | Finance use |
|---|---|---|---|
| **Library** | `import pymupdf`, `doc = pymupdf.open("file.pdf")` | open a PDF; `doc[0]` is page 1 | a company statement |
| **`.splitlines()`** | `doc[0].get_text().splitlines()` | cut the text into a list of lines | one value per line |
| **`.strip()`** | `line.strip()` | remove invisible spaces at both ends | clean lines |
| **`.replace()`** | `"73,716".replace(",", "")` | replace a piece of text by another (here, remove it) | remove commas and `$` |
| **`int()`** | `int("73716")` | turn text into a whole number | a number you can add |
| **`list.index()`** | `lines.index("Services")` | the position of the first match | find a label |

### Error types you can now read

| Error type | Meaning | Example |
|---|---|---|
| `KeyError` | no column or row with that name | `prices["Apple"]`: the column is `AAPL` |

## Session 4: solvers, to solve for the number

The one new idea: **a function can be an input to another function.** You hand over the question; the solver asks it many times.

| Python name | You write | It means | Finance use |
|---|---|---|---|
| **Import from a module** | `from scipy.optimize import brentq, minimize` | take two tools from the SciPy toolbox | the solvers |
| **Function of the unknown** | `def npv_at(r): return cash_flows[0] + discount(cash_flows[1:], r)` | a function with only the unknown as input | the question for the solver |
| **Function as an input** | `brentq(npv_at, 0, 1)` | no brackets after `npv_at`: you pass the function, not its result | the IRR |
| **Bracket** | the `0, 1` in `brentq(npv_at, 0, 1)` | search between these two values; the function must change sign between them | between 0% and 100% |
| **Bisection** | `for step in range(30): mid = (low + high) / 2 ...` | a loop that halves the interval each time | Goal Seek by hand |
| **Gap function** | `def gap(debt): return min(dscr(debt)) - covenant` | zero exactly at the answer | the covenant |
| **Change an input, re-run** | `covenant = 1.5`, then `brentq(gap, 1, 500)` again | a function reads a variable's value at the moment it runs | a stricter covenant |
| **`min()`** | `min(dscr(debt))` | the smallest value: the worst year | "1.25x in every year" |
| **Array of years** | `np.arange(1, 6)` | 1, 2, 3, 4, 5 | one value per year at once |
| **`minimize`** | `minimize(port_vol, w0, bounds=..., constraints=...)` | find the inputs that make a function as small as possible | the least risky mix |
| **Starting point** | `w0 = np.array([1/3, 1/3, 1/3])` | where the solver starts walking | equal weights |
| **Bounds** | `bounds=[(0, 1)] * 3` | each weight between 0 and 1 (Session 2: a list `* 3` repeats it) | no shorting |
| **Constraint dictionary** | `{"type": "eq", "fun": fully_invested}` | a rule the answer must meet: `fully_invested(w)` must equal 0 | weights sum to 1 |
| **Result** | `result.x`, `result.fun`, `result.success` | the answer, the value at the answer, did it work? | weights, volatility, status |
| **Package install** | `!pip install numpy-financial` | add a toolbox to Colab | `npf` |
| **Financial functions** | `npf.irr(cf)`, `npf.mirr(cf, 0.08, 0.08)` | Excel's IRR and MIRR | a second opinion |

### Error types you can now read

| Error type | Meaning | Example |
|---|---|---|
| `ValueError: f(a) and f(b) must have different signs` | the function has the same sign at both ends of the bracket: no zero to find between them | `brentq(npv_at, 0.10, 0.50)`: the NPV is negative at 10% and at 50% |
