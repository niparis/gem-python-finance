# Homework 1 - Update ten pitchbooks with Python

Individual. Due date and submission on Moodle.
**Submit:** your Colab notebook (`.ipynb`, with all outputs) and the deck `Corporation A.pptx`.

> **From:** Managing Director
>
> "We cover ten companies. Every time the price targets change, someone spends a day updating ten PowerPoint decks by hand, and makes mistakes. Make Python do it."

You will open a PowerPoint template with Python, change a number, change a table, then build ten decks in one loop.

**What you need:** variables, lists, loops and f-strings (Session 1), `pd.read_excel` and `.iloc` (Session 3), `pip install` (Session 4). New: the `python-pptx` package and a few text tools, explained below.

**AI:** allowed. Do the two warm-ups (2.1 and 2.4) without AI. In every other cell, add a comment that explains each line in your own words.

## Setup

Open a new Colab notebook, name it `Homework 1 - First Last`, and run:

```python
!pip install -q python-pptx
!wget -q https://raw.githubusercontent.com/niparis/gem-python-finance/main/data/hw1_template.pptx
import pandas as pd
from pptx import Presentation
from pptx.util import Pt
```

Check: the Colab file panel (folder icon, left) shows `hw1_template.pptx`. Download it and open it once, to see the four slides.

Put each question under its own markdown heading (`### Question 2.3`).

## Part 1 - Open the template

A PowerPoint file in Python is a tree. You go down it with dots and positions:

```
presentation -> slides[n] -> shapes[n] -> text_frame -> text
```

Positions start at 0, as in a list: slide 1 is `slides[0]`, slide 3 is `slides[2]`.

**Question 1.1.** Create a variable `prs` equal to `Presentation("hw1_template.pptx")`. Print the number of slides with `len(prs.slides)`. Expected: 4.

## Part 2 - Change one number

**Question 2.1 (warm-up on lists, no AI).** Create a list of ten numbers of your choice. Then:
- set the last element to 2,
- set the third, fourth and fifth elements to 3 (one line, with a slice),
- add 20 at the end,
- print how many elements the list has.

**Question 2.2.** Create a variable `third_slide` equal to slide 3 of `prs` (the slide "Valuation and rating").

**Question 2.3.** Write a loop over `third_slide.shapes` that prints the position of each shape and its text (`shape.text_frame.text`). Which shape holds the price target? Create a variable `text_target` equal to its text.

**Question 2.4 (warm-up on text, no AI).** Create `sentence = "Hello, world"`. Print:
- the fifth character,
- the word `Hello`, with a slice,
- the position of `w`, with `sentence.find("w")`.

**Question 2.5.** Change the price target from `$10.65` to `$12.55`:
1. `new_price = "$12.55"` and `N = len(new_price)`.
2. `str_to_find = "Price Target: "` (with the space at the end).
3. Find where the price starts: the position of `str_to_find` in `text_target`, plus the length of `str_to_find`.
4. `old_price` = the `N` characters that start there (a slice).
5. `new_text = text_target.replace(old_price, new_price)`.
6. Write it back into the shape. Write into the first **run** (a piece of text with one format), so the font stays the same:
   `third_slide.shapes[1].text_frame.paragraphs[0].runs[0].text = new_text`

Check: print the shape's text again. Only the price changed.

**Question 2.6 (in a markdown cell).** Run your steps again with `new_price = "$9.80"` on a fresh copy of the template. What do you get, and why? How would you fix it?

## Part 3 - Change a table

**Question 3.1.** On slide 4 ("Industry analysis: five forces"):
- create `n_shape`, the number of shapes on the slide,
- loop over `range(n_shape)` and print, for each position, whether the shape has a table (`shape.has_table`). Keep the position of the table.

**Question 3.2.** Get the table: `table = prs.slides[3].shapes[position].table`. Print every cell:

```python
for cell in table.iter_cells():
    print(cell.text)
```

A cell is `table.cell(row, column)`, both from 0. Change the level of "Threat of new entrants" from `Low` to `Medium`. Which row and column did you use?

Save your work with `prs.save("test.pptx")`, download it and check slides 3 and 4 in PowerPoint or Google Slides.

## Part 4 - Ten decks in one loop

**Question 4.1.** Load the data:

```python
url = "https://raw.githubusercontent.com/niparis/gem-python-finance/main/data/hw1_companies.xlsx"
targets = pd.read_excel(url, sheet_name="Targets")
descriptions = pd.read_excel(url, sheet_name="Descriptions", index_col="Company Name")
```

Display both. How many companies? What type is a price target (text or number)?

**Question 4.2.** Write one loop over the companies, `for i in range(len(targets)):`. Inside the loop:
1. Open the template again: `prs = Presentation("hw1_template.pptx")`. (Why inside the loop? Answer in a comment.)
2. `name = targets.iloc[i, 0]` and `target = targets.iloc[i, 1]`.
3. Slide 1: replace "Corporation Name" with `name` (write into the run, as in 2.5).
4. Slide 3: change the price target with your method from 2.5. The new price is text with two decimals: `f"${target:.2f}"` turns `12.5` into `$12.50`.
5. Slide 4: replace the text of the "Our view" box with `descriptions.loc[name, "Text"]`, and set its size to 14 points: `....runs[0].font.size = Pt(14)`.
6. Save: `prs.save(f"{name}.pptx")`.

Then download all ten decks at once:

```python
!zip -q decks.zip Corporation*.pptx
from google.colab import files
files.download("decks.zip")
```

## Part 5 - Check before you send

**Question 5.1. Put the answer back in.** Write a loop that opens each saved deck again and prints the company name from slide 1 and the price target text from slide 3, next to the values in `targets`. They must match for all ten. Make the loop stop with an error if one does not:
`if expected not in text: raise ValueError(f"{name}: wrong price target")`

**Question 5.2. Look at it.** Open `Corporation A.pptx` and `Corporation J.pptx`. Is the title still large? Does the "Our view" text fit in its box? Write what you see in a markdown cell.

## How it is graded

Correct decks, checks that pass, and clear comments.
