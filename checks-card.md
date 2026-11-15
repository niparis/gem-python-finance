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
