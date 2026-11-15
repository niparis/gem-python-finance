# Homework 3 - Is Apple's EPS growth real?

Individual. Due date and submission on Moodle.
**Submit:** your Colab notebook (`.ipynb`, with all outputs) and your Excel file.

> **From:** Managing Director
>
> "Apple's EPS keeps going up. Is that the business growing, or just buybacks? And what is driving the profit? Short answer, with numbers I can trust."

In Session 0 you saw a notebook pull Apple's diluted EPS from the SEC. This homework goes one level deeper: where the profit comes from, why EPS grows, and whether the cash backs it up. Then you compare with Microsoft.

**What you need:** reading a PDF (Session 3, notebook 3c), pandas (Session 3), the Session 0 demo notebook (you may start from it). New tools you will need are listed at the end, with examples.

**AI:** allowed. Explain every line in a comment, in your own words. Copying AI code is not enough: your checks must pass and your email must answer the question.

Put each part under its own markdown heading (`## Part 3`).

## The data

- **Apple's quarterly statement PDFs**, FY23 Q3 to FY26 Q4 (14 quarters), in the course repo:
  `https://raw.githubusercontent.com/niparis/gem-python-finance/main/data/apple_statements/apple_fy25_q4.pdf` (change `fy25_q4` for each quarter).
  Download one with `!wget -q <url>`. Apple's fiscal year ends in late September: Q1 is October to December.
- **The SEC company facts API**, as in the Session 0 demo: one JSON file per company with every number it reported.
  Apple: `CIK0000320193`. Microsoft: `CIK0000789019`. The SEC asks for a `User-Agent` header with a contact: use the one in the demo.

## Part 1 - Read the PDFs: where does the profit come from?

For each of the 14 quarters, read from the PDF (statement of operations, the **three months** column):
- net sales of Products and of Services,
- cost of sales of Products and of Services.

Build one table, one row per quarter. Add: gross margin of Products, gross margin of Services, and the share of gross profit that comes from Services. Draw the two margins over time.

Why the PDFs: the SEC data gives total revenue, not the split between Products and Services. An analyst reads the filings.

## Part 2 - Pull the SEC data

For Apple, for the last 8 fiscal years (FY2019 to FY2026), get from the SEC data:

| Line | Example SEC name |
|---|---|
| Revenue | `RevenueFromContractWithCustomerExcludingAssessedTax` |
| Net income | `NetIncomeLoss` |
| Diluted shares | `WeightedAverageNumberOfDilutedSharesOutstanding` |
| Diluted EPS | `EarningsPerShareDiluted` |
| Operating cash flow | `NetCashProvidedByUsedInOperatingActivities` |
| Capex | `PaymentsToAcquirePropertyPlantAndEquipment` |
| Buybacks | `PaymentsForRepurchaseOfCommonStock` |
| Dividends | `PaymentsOfDividends` |

Keep annual values only (10-K, a period of about one year), one per year. Careful: the same number appears in several filings, and the field `fy` is the year of the **filing**, not of the number. Use the period's end date.

## Part 3 - Split EPS growth in two

EPS = net income / diluted shares. So each year:

(1 + EPS growth) = (1 + net income growth) / (1 + share count growth)

For each year, show EPS growth, net income growth, and share count growth (negative when buybacks reduce the shares). Then answer: how much of Apple's EPS growth comes from the business, and how much from buying back shares?

## Part 4 - Is it backed by cash?

For each year:
- free cash flow (FCF) = operating cash flow - capex,
- cash conversion = FCF / net income,
- cash returned to shareholders = buybacks + dividends, as a share of FCF.

## Part 5 - Microsoft

Repeat Parts 2 and 3 for Microsoft, and compare with Apple in one table and one chart. Microsoft's fiscal year ends in June, Apple's in September. Say in a markdown cell how you make the comparison fair (Session 0: fiscal year or calendar year?). Expect at least one SEC name to differ from Apple's.

## Part 6 - Checks

Each check is a cell that prints the result and stops with an error if it fails.
1. **Recompute EPS:** net income / diluted shares must match the reported diluted EPS, within one cent, every year.
2. **PDF vs SEC:** Products + Services net sales from your PDFs must equal total revenue. For the full years FY24, FY25 and FY26, the four quarters must add up to the SEC annual revenue.
3. **The parts add up:** Products gross profit + Services gross profit = total gross profit in the PDF.
4. **One year by hand:** pick one year and match three of your numbers against Apple's 10-K on sec.gov. Write which page and line.

## Part 7 - Deliver

- One chart that answers the MD's question.
- One Excel file, one sheet per table (quarters from the PDFs, Apple by year, Microsoft by year).
- An email to the MD, 150 words or fewer, in a markdown cell. It must answer: business growth or buybacks? What drives the profit? Is the cash there? Give the numbers.

## New tools you will probably need

| You want to | You write |
|---|---|
| Keep rows that meet a condition | `df[df["form"] == "10-K"]`; two conditions: `df[(df["form"] == "10-K") & (df["days"] > 350)]` |
| Remove repeated rows | `df.drop_duplicates(["start", "end"], keep="last")` (keep the latest filing) |
| The year of a date | `df["end"].dt.year` (after `pd.to_datetime`) |
| Put tables side by side by year | `pd.merge(apple, msft, on="year", suffixes=("_aapl", "_msft"))` |
| Read many PDFs | a loop over a list of quarters, one dictionary per quarter, then `pd.DataFrame(list_of_dicts)` |
| Several sheets in one Excel file | `with pd.ExcelWriter("hw2.xlsx") as w: table.to_excel(w, sheet_name="Apple")` |

## How it is graded

Correct numbers, checks that pass, an email that answers the question.
