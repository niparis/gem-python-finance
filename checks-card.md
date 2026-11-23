# Checks card

Code that runs is not code that is right. Python, Excel and AI will all hand you a wrong number without a warning. This card is your list of quick ways to catch it before your MD does.

It grows every session. Before you send a number, pick two or three checks that fit and run them. Each one takes a minute.

## Session 1

1. **Put the answer back in.**
   Run your calculation backwards. If you computed a present value, grow it forward again: you should land exactly on the starting amount. If you don't, something is off.
   *In class:* 1,000 in 5 years at 7% is worth 712.99 today. Grow 712.99 at 7% for 5 years and you get 1,000 back.

2. **Compare with Excel, and know its habits.**
   When Python and Excel disagree, don't pick the answer you like. Find out why. Usually one tool follows a convention you didn't know about.
   *In class:* Excel's `=NPV(3%, all five)` gives 2,256.66 and Python gives 2,324.36. Excel treats the first cash flow as year 1, not today. The fix: `=B2 + NPV(3%, B3:B6)`.

3. **Try an extreme case.**
   Push an input to its limit and see what happens. A good model stops with a clear message. A bad one quietly gives you a crazy number.
   *In class:* if growth equals the required return (g = r), the Gordon formula divides by zero. Add a guard so the code stops and says why: `if g >= r: raise ValueError(...)`.

4. **Two ways to the same number must agree.**
   If you can get a number two different ways, do it. When they disagree, at least one is wrong. Sometimes two mistakes hide each other.
   *In class:* company A came out at 7.69 in one notebook and 8.54 in another. The labels were swapped and so were two inputs. No error message anywhere.

## Session 2

5. **Use a case where you already know the answer.**
   Find a situation simple enough to answer in your head, run the code on it, and compare. If the code misses an answer you know, it will miss the ones you don't.
   *In class:* if every correlation is 1, diversification does nothing, so the portfolio's volatility is the weighted average of the funds' vols: 21.5%. The AI's code in 2b gave 46.4%: one `np.diag(vol)` was missing.
   A **range check** works the same way: a weighted average must sit between the smallest and the largest value. A portfolio return of 2.5% from funds returning 5% to 9% is impossible.

6. **Same seed, same answer.**
   Random results change every run unless you fix the seed. If nobody can reproduce your number, nobody can check it, including you next week.
   *In class:* with `np.random.default_rng(42)` the chance of a loss is 40.5% every time. Without a seed: 40.5%, then 41.3%, then 39.8%.

7. **Check the rules, not only the number.**
   A result can look great and still break a rule of the real world. Read the inputs the code chose, not just the headline output.
   *In class:* weights sum to 1 and the volatility is 12.0%, lower than any single fund. Looks perfect. But fund C is at -22%: a short position the client is not allowed to take.

8. **Race a slow, simple method against the clever one.**
   Try every option with a plain loop, then compare with the fast method. If they disagree, the difference tells you what the clever method assumed.
   *In class:* the formula said 72 / 50 / -22. A loop over 5,151 long-only portfolios said 65 / 35 / 0 at 12.56%. Same question, different rules, different answer.

## Session 3

9. **Check the dates: first, last, how many, which order.**
   Dates fail silently: a wrong format gives no error, just wrong answers. Print the first date, the last date and the number of rows, and check they make sense. Also check the order: many exports put the newest day first, and then every return is computed backwards.
   *In class:* `01/02/2024` read month-first becomes 2 January, so all twelve months land in January. With `dayfirst=True`: 1 January to 1 December, 12 rows. And with newest first, Apple seems to lose 0.055% a day instead of gaining 0.087%.

10. **Know your data's units and habits.**
    Before you use a column, ask: what unit, what period, how many days in a year? Getting this wrong gives a number that looks fine and is wrong.
    *In class:* annual volatility is `std() * 252 ** 0.5`, because a year has 252 trading days: 28.1% for Apple. With 365 you get 33.8%. And Apple's statement is "in millions": 73,716 means 73.7 billion dollars.

11. **The parts must add up to the total.**
    Most reports print their own totals. Add up your parts and compare. It is the fastest way to catch a wrong row or a wrong column.
    *In class:* Products 73,716 + Services 28,750 = 102,466, exactly the total in Apple's PDF. A reader that grabbed last year's Services column (24,972) got 98,688, and this check caught it.

12. **Two sources must agree.**
    If the same number exists in two places, pull it from both. When they match, you can trust it. When they don't, find out why before you go on.
    *In class:* the full-year total in Apple's PDF, 416,161, is exactly Apple's FY2025 revenue in the SEC data.

## Session 4

13. **A solver gives you one answer. Ask if there are others.**
    A solver stops at the first answer it finds, and does not tell you about the others. Plot the function, or try a different starting range.
    *In class:* the mine (pay 17,000, earn 16,000 a year for four years, then pay 52,000 to clean up) has two IRRs, 6.77% and 65.4%. `npf.irr` shows only the first, with no warning. When cash flows change sign more than once, decide with the NPV instead: +604 at 8%, so invest.

14. **Read the solver's status, not just its number.**
    Every solver reports whether it worked. If you only read the number, you can present a failure as a result.
    *In class:* `minimize` with no rule that weights sum to 1 answers 0 / 0 / 0 with 0% risk: "invest nothing". `result.success` says False.

15. **Solve a small version by hand.**
    Shrink the problem until you can do it on paper or with Excel's Goal Seek, then check the code gives the same answer. Then come back to the full problem.
    *In class:* debt capacity with year 1 only: 15.75 / (1.25 x (1/5 + 7%)) = 46.67, the same as `brentq`. But the covenant applies every year: with a bad year 2, the right answer drops to 38.76.

16. **Ask: would I believe this if a colleague showed me?**
    Every check can pass and the answer can still be useless. An optimizer leans hardest on your weakest input, so step back and ask if the result makes sense.
    *In class:* the best risk-adjusted mix of the five mega-caps puts 83% in Apple, only because Apple did best over the last five years. Past returns are not a forecast.
