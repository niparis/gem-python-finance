# Homework 2 - What is the option to expand worth?

Individual. Due date and submission on Moodle.
**Submit:** your Colab notebook (`.ipynb`, with all outputs).

> **From:** Managing Director
>
> "The client wants to enter a new market. The NPV says no. But if the market takes off, they can invest a lot more later. What is that chance worth?"

A plain NPV assumes the company decides everything today. In real life it can wait and invest more if things go well. That right, without the obligation, is a **real option**. You will price it the way banks price options: simulate thousands of futures, take the payoff in each, discount, and average.

**What you need:** functions and NPV (Session 1), random numbers with a seed and arrays (Session 2), annual volatility from prices (Session 3). New: `np.exp`, and the option payoff, explained below.

**AI:** allowed. Explain every line in a comment, in your own words.

Put each question under its own markdown heading (`### Question 3`).

## Part 1 - Price a call option

A **call option** is the right to buy an asset at a fixed price (the **strike**, K) on a fixed date (the **maturity**, T). Example: a call on Apple with strike $230. At maturity, if Apple trades at $240, you buy at $230 and sell at $240: you make $10. If Apple trades at $220, you do nothing: you make 0.

So at maturity the payoff is: max(0, S_T - K), where S_T is the price at maturity.

**Question 1.** Write a function `call_payoff(S, K)` that returns the payoff. Test it: `call_payoff(240, 230)` gives 10, `call_payoff(220, 230)` gives 0.

We do not know S_T today. A standard model says the price at maturity is:

S_T = S_0 × exp( (r - σ²/2) × T + σ × √T × z )

- S_0: the price today
- r: the risk-free rate; σ: the volatility (both per year); T: years to maturity
- z: a random number from a standard normal distribution (mean 0, standard deviation 1)
- `np.exp(x)` is e to the power x: continuous compounding

The first part is a steady drift. The second part is the random shock: larger when volatility is high and when maturity is far.

**Question 2.** Write a function `stock_at_maturity(S0, r, sigma, T, z)` that returns S_T for a given z.

**Question 3.** Simulate N = 100,000 futures:
- create the generator with a seed, `rng = np.random.default_rng(42)`, and draw N values of z with `rng.standard_normal(N)`,
- compute S_T for each (your function works on a whole array at once),
- compute the payoff of each. Note: `max()` works on two numbers; for an array use `np.maximum(0, ST - K)`.

**Question 4.** The option price is the average payoff, discounted to today: payoff today = payoff at maturity × exp(-r × T). Compute the price.

**Check:** with S_0 = $100, K = $100, σ = 10%, r = 5%, T = 1 year, the price is about **$6.80**. If you are far from it, find out why before you go on.

**Question 4b (in a markdown cell).** Run it with N = 1,000 and N = 100,000, with and without the seed. What changes, and which N would you trust?

## Part 2 - The project

The company invests $500m today. Cash flows: $25m in years 1 and 2, $100m in year 3, $150m in years 4 and 5. Discount rate: 5%.

**Question 5.** Write a function that returns the NPV of the project from a list of cash flows and a rate. What is the NPV? Would you invest, on this number alone?

## Part 3 - The option to expand

Within five years the company can scale up: it would invest $1bn and expects revenue worth $750m today (in present value). Today that is a bad deal. But the $750m is uncertain, and if the market grows it can be worth much more than $1bn. The company only invests if it is. This is a call option:

| Option | Here |
|---|---|
| S_0, today's value | $750m |
| K, the strike | $1,000m, the investment |
| T, maturity | 5 years |
| r | 5% |
| σ, volatility | the average volatility of five listed peers |

**Question 6.** Compute the peers' volatility. The file has one sheet per peer, daily prices over about five years (column `Date` is the day number).

```python
url = "https://raw.githubusercontent.com/niparis/gem-python-finance/main/data/hw2_peers.xlsx"
sheets = pd.read_excel(url, sheet_name=None)   # all sheets: a dictionary, sheet name -> DataFrame
for name, sheet in sheets.items():
    print(name, sheet.shape)
```

For each peer, compute the annual volatility of daily returns (Session 3: 252 trading days). Store the five in a dictionary or a Series, then take the average.

**Check:** each volatility is between 15% and 60% (a listed company); you have five, not four or six.

**Question 7.** Price the option with your Monte Carlo code from Part 1. Add it to the NPV of Question 5. Is the project now worth doing?

**Question 8. Change an input, re-run.** Price the option with the volatility 10 points lower, then 10 points higher. Write two or three sentences for the MD: is the decision sensitive to volatility? Why does more uncertainty make the option worth more?

## How it is graded

Correct numbers, checks that pass, a clear answer to the MD.
