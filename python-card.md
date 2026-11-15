# Python card

One page, one section per session. Keep it open while you work. It grows every week.

## Session 1: first Python, through finance

| You write | It means | Finance use |
|---|---|---|
| `rate = 0.07` | a named value | an assumption |
| `(1 + rate) ** years` | `**` is "to the power of" | compounding, discounting |
| `f"PV = {pv:,.2f}"` | text with a number inside | a readable output |
| `def present_value(...):`<br>`    return ...` | a reusable formula | PV, FV, NPV |
| `cash_flows[0]` | a position, counted from 0 | year 0 = position 0 |
| `for t, cf in enumerate(cash_flows):` | do this for every item, with its position | discount each cash flow |
| `if npv > 0: ... else: ...` | decide | invest or not |
| `{"d0": 0.27, "r": 0.12}` | a dictionary: named inputs | the assumptions tab |

### Functions that work together

One function per step, chained into a value. Each step can be checked alone, like one Excel block per calculation.

```
dividends()  ->  terminal_value()  ->  discount()  ->  assessment()  ->  "overvalued"
```

- **`if / elif / else`**: three outcomes (undervalued, fair, overvalued).
- **A guard**: `if g >= r:` stop with a clear message. Gordon growth needs r > g.
- `enumerate(values, start=1)` counts from 1. Use it when the first value is year 1.

### Errors you can now read

Read the last line first, then find the arrow `---->`, then ask: which value is not what I think?

| Error | Meaning | Example |
|---|---|---|
| `TypeError` | wrong kind of value | `1 + "7%"`: a number and text |
| `ZeroDivisionError` | divided by 0 | `r - g` when g = r |
