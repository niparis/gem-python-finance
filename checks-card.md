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
