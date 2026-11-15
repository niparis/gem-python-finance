# Python card

Every piece of Python we use, with its name, what it does in plain words, and where you used it. Keep it open while you work: when you meet a line you can't read, look it up here. Knowing the name helps too, because it is what you type when you ask AI or search for help.

It grows every session.

## Session 1: first Python, through finance

You wrote your first formulas: present value, NPV, a dividend model. Most of it is Excel thinking with new words.

| Python name | You write | What it does | Where you used it |
|---|---|---|---|
| **Variable** | `rate = 0.07` | Gives a name to a value, so you can use the name everywhere. Change it once, and everything that uses it follows. | Every assumption: rate, growth, years |
| **Operator `**`** | `(1 + rate) ** years` | "To the power of", like `^` in Excel. | Compounding and discounting |
| **f-string** | `f"PV = {pv:,.2f}"` | Text with a number inside. The part after `:` sets the format: `,.2f` means thousands separators and 2 decimals. | Printing results people can read |
| **Function** | `def present_value(...):`<br>`    return ...` | A formula you write once and reuse with different inputs. The inputs go in the brackets; `return` sends back the result. | PV, FV, NPV |
| **List + index** | `cash_flows[0]` | Values in order. Positions start at 0, not 1: `[0]` is the first value. | Year 0 is position 0 |
| **For loop** | `for t, cf in enumerate(cash_flows):` | Repeats the indented lines once for every item. `enumerate` also gives you each item's position. | Discounting each cash flow |
| **Condition** | `if npv > 0: ... else: ...` | Runs one block or the other, depending on a test. | Invest or not |
| **Dictionary** | `{"d0": 0.27, "r": 0.12}` | Values you look up by name (the key) instead of by position. | The assumptions tab of a model |

### Functions that work together

Big models are small functions chained together, each doing one step. You can check each step on its own, like one Excel block per calculation.

```
dividends()  ->  terminal_value()  ->  discount()  ->  assessment()  ->  "overvalued"
```

| Python name | You write | What it does |
|---|---|---|
| **Condition with three outcomes** | `if ...: ... elif ...: ... else: ...` | Tests one thing, then the next, then falls back to `else`. Here: undervalued, fair or overvalued. |
| **Guard** | `if g >= r: raise ValueError("...")` | Stops the code on purpose, with your own message, when an input makes no sense. No answer is better than a wrong one. |
| **`enumerate` from 1** | `enumerate(values, start=1)` | Counts from 1 instead of 0. Use it when your first value is year 1. |

### Reading an error message

Errors look scary but they are trying to help. Read the **last line** first: it names the problem. Then find the arrow `---->`: it points at the line that broke. Then ask: which value is not what I think it is?

| Error type | What it means | Example |
|---|---|---|
| `TypeError` | You mixed kinds of values that don't go together | `1 + "7%"`: a number plus text |
| `ZeroDivisionError` | You divided by 0 | `r - g` when g = r |
| `ValueError` | A value makes no sense (here, your own guard said so) | g >= r in the Gordon growth model |

## Session 2: NumPy, through portfolio risk

Lists hold values; arrays compute with them, all at once. One line of NumPy replaces a whole loop, and a whole column of Excel formulas.

| Python name | You write | What it does | Where you used it |
|---|---|---|---|
| **Import** | `import numpy as np` | Loads a toolbox, under the short name `np`. | Every notebook from now on |
| **Array** | `np.array([0.2, 0.3, 0.5])` | Like a list, but you can do maths on all the values at once. | Weights, vols, returns |
| **Vectorized operation** | `w * ret` | Multiplies value by value, no loop needed. | Each fund's share of the return |
| **Sum** | `np.sum(w * ret)` | Adds up all the values. | Portfolio return |
| **Matrix product** | `w @ cov @ w` | Rows times columns, in one step: Excel's `MMULT`. | Portfolio variance |
| **2D array + index** | `corr[0, 2]` | A table of numbers. Pick a value with `[row, column]`, both from 0. | Correlation of fund A and fund C |
| **Diagonal matrix** | `np.diag(vol)` | Puts values on the diagonal, 0 everywhere else. | Building the covariance matrix |
| **Square root** | `np.sqrt(var)` | The square root. | Variance to volatility |
| **Random generator + seed** | `np.random.default_rng(42)` | Makes random numbers. The seed (42) makes them the same every run, so others can check your work. | Simulations |
| **Shape** | `size=(252, 5000)` | How many rows and columns to make. | 252 days x 5,000 paths |

### More NumPy from the notebooks

| Python name | You write | What it does | Where you used it |
|---|---|---|---|
| **Product down a column** | `np.prod(1 + daily, axis=0)` | Multiplies all the rows of each column together. `axis=0` means "down the columns". | Value after one year, for each path |
| **Running product** | `np.cumprod(1 + daily, axis=0)` | Same, but keeps every step along the way. | The fund's value day after day |
| **Median, percentile** | `np.median(final)`, `np.percentile(final, 5)` | The middle value; the value that 5% of results fall below. | A typical year; a bad year |
| **Comparison + mean** | `(final < 100).mean()` | Tests every value (True or False), then the mean counts the share of True. | The chance of a loss |
| **Ones, full, fill diagonal** | `np.ones((3, 3))`, `np.full((3, 3), 0.9)`, `np.fill_diagonal(m, 1.0)` | Builds an array filled with one value, then sets the diagonal. | A known case; crisis correlations |
| **Round** | `np.round(cov, 4)` | Shows 4 decimals, easier to read. | A readable matrix |

### Functions on arrays

The same function works for any portfolio: change the inputs, not the code. The guard stops it if the weights don't add up to 1.

```
def portfolio_vol(weights, vol, corr):
    if round(np.sum(weights), 6) != 1:
        raise ValueError("Weights must sum to 1.")
    cov = np.diag(vol) @ corr @ np.diag(vol)
    return np.sqrt(weights @ cov @ weights)
```

| Python name | You write | What it does | Where you used it |
|---|---|---|---|
| **Function** | `def portfolio_vol(weights, vol, corr):` | One formula, for any weights you pass in. | Return and risk of any portfolio |
| **Guard** | `if ...: raise ValueError(...)` | Stops the code if the weights don't sum to 1. | No result is better than a wrong one |
| **round** | `round(np.sum(weights), 6)` | Rounds before comparing. Computers store decimals slightly off: `0.7 + 0.2 + 0.1` gives 0.9999999999999999. Never test decimals with `==` directly. | The weights test |
| **Known case** | `corr = np.ones((3, 3))` | Inputs where you already know the answer, to test the function. | All correlations at 1 give the weighted average |

## Session 3: pandas, through real prices and a PDF

pandas is Excel inside Python: tables with named columns and rows. You loaded five years of real prices, built a risk table, and read numbers out of a PDF.

| Python name | You write | What it does | Where you used it |
|---|---|---|---|
| **Import** | `import pandas as pd` | Loads the table toolbox, as `pd`. | Prices, returns |
| **DataFrame** | `pd.read_excel("peers.xlsx", index_col="Date")` | A table, like an Excel sheet: an index (row labels), columns, rows. | Five years of prices |
| **Index** | `prices.index` | The labels of the rows. Here, the dates. | One row per trading day |
| **Column** | `prices["AAPL"]` | One column, picked by its name. | One stock |
| **Row by label** | `prices.loc["2026-11-13"]` | One row, picked by its label. | One day, all stocks |
| **Row by position** | `prices.iloc[0]`, `prices.iloc[-1]` | One row, picked by its position. `0` is the first, `-1` the last. | The first and the last day |
| **Method** | `prices.pct_change().dropna()` | An action on the table. You can chain them: each one works on the result of the one before. | Daily returns |
| **Statistic per column** | `returns.std()`, `returns.corr()`, `returns.cov()` | Computes one result for each column at once. | Volatility, correlation, covariance |
| **Resample** | `prices.resample("YE").last()` | Groups the rows by period (here, year end) and keeps one per period. | Year-by-year returns |
| **Rolling window** | `returns.rolling(63).std()` | Looks at the last 63 rows, then slides one day forward, again and again. | How risk changes over time |
| **Chart** | `rebased.plot()` | Draws one line per column. | The "rebased to 100" chart |
| **Export** | `vol.to_excel("vol.xlsx")` | Writes the table to an Excel file. | Sending it to the MD |
| **DataFrame from columns** | `pd.DataFrame({"Volatility": vol, "Beta": beta})` | Builds a table from a dictionary: each key becomes a column. | The risk table |
| **Sort** | `table.sort_values("Sharpe", ascending=False)` | Orders the rows, here best first. | Ranking by risk-adjusted return |
| **Round** | `table.round(3)` | A shorter copy of the table, easier to read (0.196 is 19.6%). | A readable table |
| **Dates, day first** | `pd.read_csv("file.csv", parse_dates=["Date"], dayfirst=True)` | Reads `01/02/2024` as 1 February, the European way. | European dates |

### Reading a PDF

A PDF gives you text, line by line. These tools turn that text into numbers you can add up.

| Python name | You write | What it does | Where you used it |
|---|---|---|---|
| **Library** | `import pymupdf`, `doc = pymupdf.open("file.pdf")` | Opens a PDF. `doc[0]` is page 1. | Apple's statement |
| **`.splitlines()`** | `doc[0].get_text().splitlines()` | Cuts the page's text into a list of lines. | One value per line |
| **`.strip()`** | `line.strip()` | Removes the invisible spaces at both ends of a line. | Clean lines |
| **`.replace()`** | `"73,716".replace(",", "")` | Swaps one piece of text for another; here, removes the comma. | Removing commas and `$` |
| **`int()`** | `int("73716")` | Turns text into a whole number you can calculate with. | A number you can add |
| **`list.index()`** | `lines.index("Services")` | Finds the position of the first line that matches. | Finding a label |

### New error

| Error type | What it means | Example |
|---|---|---|
| `KeyError` | There is no column or row with that name | `prices["Apple"]`: the column is called `AAPL` |

## Session 4: solvers, to solve for the number

Goal Seek, done properly. You give Python the target; it finds the input.

The one new idea: **a function can be an input to another function.** You hand over the question; the solver asks it again and again until it finds the answer.

| Python name | You write | What it does | Where you used it |
|---|---|---|---|
| **Import from a module** | `from scipy.optimize import brentq, minimize` | Takes just two tools from the SciPy toolbox. | The solvers |
| **Function of the unknown** | `def npv_at(r): return cash_flows[0] + discount(cash_flows[1:], r)` | A function whose only input is the thing you are looking for. | The question you ask the solver |
| **Function as an input** | `brentq(npv_at, 0, 1)` | No brackets after `npv_at`: you pass the function itself, not its result. | The IRR |
| **Bracket** | the `0, 1` in `brentq(npv_at, 0, 1)` | Where to search, between two values. The function must change sign (+ to -) between them. | Between 0% and 100% |
| **Bisection** | `for step in range(30): mid = (low + high) / 2 ...` | Cut the search range in half, keep the half where the answer is, repeat. | Goal Seek by hand |
| **Gap function** | `def gap(debt): return min(dscr(debt)) - covenant` | A function that is exactly 0 at the answer you want. | The covenant |
| **Change an input, re-run** | `covenant = 1.5`, then `brentq(gap, 1, 500)` again | A function reads a variable's value when it runs, so changing the variable changes the answer. | A stricter covenant |
| **`min()`** | `min(dscr(debt))` | The smallest value: here, the worst year. | "1.25x in every year" |
| **Array of years** | `np.arange(1, 6)` | 1, 2, 3, 4, 5. Stops before the second number. | One value per year, all at once |
| **`minimize`** | `minimize(port_vol, w0, bounds=..., constraints=...)` | Finds the inputs that make a function as small as possible. | The least risky mix |
| **Starting point** | `w0 = np.array([1/3, 1/3, 1/3])` | Where the solver starts looking. | Equal weights |
| **Bounds** | `bounds=[(0, 1)] * 3` | Each weight must stay between 0 and 1. (`* 3` repeats the list, Session 2.) | No short selling |
| **Constraint dictionary** | `{"type": "eq", "fun": fully_invested}` | A rule the answer must meet: `fully_invested(w)` must equal 0. | Weights sum to 1 |
| **Result** | `result.x`, `result.fun`, `result.success` | The answer, the value at the answer, and whether it worked. Always read `.success`. | Weights, volatility, status |
| **Package install** | `!pip install numpy-financial` | Adds a toolbox Colab doesn't have yet. | `npf` |
| **Financial functions** | `npf.irr(cf)`, `npf.mirr(cf, 0.08, 0.08)` | Excel's IRR and MIRR, in Python. | A second opinion |

### New error

| Error type | What it means | Example |
|---|---|---|
| `ValueError: f(a) and f(b) must have different signs` | The function has the same sign at both ends of your bracket, so there is no zero to find between them. Widen or move the bracket. | `brentq(npv_at, 0.10, 0.50)`: the NPV is negative at 10% and at 50% |
