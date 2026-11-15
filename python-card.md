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
