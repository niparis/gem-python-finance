# Checks card

How to know a number is right. One page, one section per session. Use it before you trust any result.

## Session 1

1. **Put the answer back in.** Reverse the calculation: it must give the starting point.
   Example: the future value of the PV (712.99 at 7% for 5 years) is 1,000.
2. **Compare with Excel, and know its conventions.** When two tools disagree, find out why. Never pick the one you like.
   Example: Excel's `=NPV(3%, all five)` gives 2,256.66 because it treats the first value as year 1. Python gives 2,324.36. The fix: `=B2 + NPV(3%, B3:B6)`.
3. **Try an extreme case.** A model must fail loudly, not give a silly number.
   Example: growth equal to the required return (g = r) divides by zero. Add a guard: `if g >= r:` stop with a clear message.
4. **Two calculations of the same thing must agree.** If they do not, one has an error, maybe two that hide each other.
   Example: company A is 7.69 in one notebook and 8.54 in another. Swapped labels plus swapped arguments, no error message.

## Session 2

5. **Known case.** Find a situation where you know the answer without the code. If the code does not give it, the code is wrong.
   Example: all correlations equal to 1 gives the weighted average of the vols: 21.5% for the client portfolio.
6. **Same seed, same answer.** Random results must be repeatable, or nobody can check them.
   Example: `np.random.default_rng(42)` gives a 43.5% chance of a loss every time. Without a seed: 43.5%, 44.4%, 43.8%.
7. **Check the constraints, not only the number.** A result can pass the easy tests and still break a rule of the real world.
   Example: weights sum to 1 and the vol is 12.0%, below every single fund. But fund C is at -22%: a short position the client cannot take.
8. **Brute force against the clever method.** Try a slow, simple method and compare with the fast one. If they disagree, find the difference.
   Example: the formula gives 72 / 50 / -22 at 12.0%. A loop over 5,151 long-only portfolios gives 65 / 35 / 0 at 12.56%. The spec decided the answer.

## Session 3

9. **Check the dates: first, last, count, oldest first.** Dates fail silently: a wrong format gives no error, only a wrong answer. Many data exports are newest first, and `.pct_change()` then computes every return backwards.
   Example: `01/02/2024` read month-first is 2 January: all twelve months land in January and the second half of 2024 has 0 rows. With `dayfirst=True`: first date 1 January, last date 1 December, 12 rows. Newest first: Apple's average daily return is -0.055% instead of +0.087%.
10. **Know the source's units and conventions.** Which column, which units, how many days in a year.
    Example: annual volatility is `std() * 252 ** 0.5`: 252 returns a year, 28.1% for Apple, not 33.8% with 365. Apple's statement is "in millions": 73,716 is 73.7 billion dollars.
11. **The parts must add up to the total.** Most reports print their own total. Your parts must reproduce it.
    Example: Products 73,716 + Services 28,750 = 102,466, the total net sales printed in the PDF. A reader that took last year's column (24,972) gives 98,688 and fails the check.
12. **Two sources must agree.** Two independent sources of the same number: if they differ, find out why.
    Example: the twelve-month total in Apple's PDF, 416,161, equals Apple's FY2025 revenue in the SEC data.

## Session 4

13. **A solver gives one answer: ask if there are others.** Plot the function, try other brackets or starting points.
    Example: the mine (-17,000, then 16,000 for four years, then -52,000) has two IRRs, 6.77% and 65.4%. `npf.irr` shows only the first, with no warning. When the cash flows change sign more than once, decide with the NPV (+604 at 8%: invest).
14. **Read the solver's status, not only its number.** Every solver says whether it worked.
    Example: `minimize(port_vol, w0)` with no constraint returns weights 0 / 0 / 0 and a volatility of 0%: "invest nothing". `result.success` is False.
15. **Solve a simple version by hand.** Find a case small enough to do on paper, or in Excel with Goal Seek.
    Example: debt capacity, year 1 only: 15.75 / (1.25 x (1/5 + 7%)) = 46.67, the same as `brentq`. But check every year: with a downturn in year 2, the right answer is 38.76.
16. **Is it plausible?** An optimizer pushes hardest on your weakest input. Every check can pass and the answer can still be useless.
    Example: the best risk-adjusted mix of the five mega-caps puts 83% in Apple, because Apple did best in the last five years. Past returns are not forecasts.
