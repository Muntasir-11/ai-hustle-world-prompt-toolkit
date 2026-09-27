# The W.O.R.T.H. Score

AI Hustle World's single scoring system for tool reviews and comparisons. Five weighted dimensions, each rated 0–5 against evidence, rolled up into one score out of 100. The same weights and the same formula are used in every review and comparison, so a score in one article means the same thing as a score in another.

## Why this exists

A different scorecard in every article can't be compared across articles, and a reader has to learn a new system each time. W.O.R.T.H. replaces the earlier AHW 5-point review scores and the Decision-Fit Score with one system. It keeps what worked in both: the Decision-Fit Score's rule that every rating rests on documented evidence, and the review scores' focus on what a buyer actually pays and gets.

The name is also the question it answers: is this tool worth it *for the job you are hiring it for*? A tool can score well on one job and poorly on another. That is a correct result, not a flaw in the score.

## The five dimensions

| Letter | Dimension | Weight | What it measures |
|---|---|---|---|
| **W** | Workflow fit | 30% | How completely the tool does the job it is being scored for, and how many of that job's steps it covers without leaving the product |
| **O** | Openness | 15% | Imports, exports, integrations and APIs; how hard it is to move your work, files or data out later |
| **R** | Real cost | 20% | What a realistic workload costs per month, and whether plans, credits and limits are clear enough to predict that cost before you buy |
| **T** | Trust | 20% | How well official documentation backs the vendor's claims, plus data handling, licensing and support |
| **H** | Human control | 15% | Editing, review, approval and override: how easily a person can check and correct what the tool produces |

**Formula:** W.O.R.T.H. Score = Σ (rating ÷ 5 × weight), giving a total out of 100.

A tool rated 4, 3, 4, 5, 3 scores (4/5×30) + (3/5×15) + (4/5×20) + (5/5×20) + (3/5×15) = 24 + 9 + 16 + 20 + 9 = **78**.

## Rating each dimension (0–5)

| Rating | Meaning |
|---|---|
| 5 | Extensively and clearly documented, or confirmed by saved first-hand testing, with no significant gaps |
| 4 | Well supported, with minor gaps or caveats |
| 3 | Adequate for most buyers, but with a limitation a careful reader should know about |
| 2 | Weak, or supported only by vendor claims that could not be checked |
| 1 | Largely missing or contradicted by other evidence |
| 0 | No evidence found for this dimension |

## Scoring rules

1. **Name the job first.** Every score is for a stated job ("clip extraction from long-form video", "complete local chat app"). If a tool is scored against a job it wasn't built for, say so next to the score.
2. **Every rating needs a source.** Record the page, document or test artifact behind each rating. A rating that can't be traced to a source drops to 2 at most.
3. **State the evidence basis.** Label each score *Documented* (built from official documentation and independent data) or *Tested* (backed by saved, dated first-hand test artifacts). Never label a score *Tested* without the artifact.
4. **Cost the realistic workload.** For R, calculate the cost of a stated monthly workload, not just the entry price. Use the [AI Tool Pricing Database](https://github.com/Muntasir-11/ai-tool-pricing-database) rows where the tool is covered, and put units side by side (credits vs media hours vs characters) instead of comparing sticker prices.
5. **Keep the weights fixed.** Don't reweight for a single article. If a job needs a different emphasis, explain it in the article's prose.
6. **Show the arithmetic.** Publish the per-dimension ratings with the total so readers can check the sum.

## Publishing template

| Tool | W (30) | O (15) | R (20) | T (20) | H (15) | W.O.R.T.H. | Basis |
|---|---|---|---|---|---|---|---|
| [Tool A] | [x] | [x] | [x] | [x] | [x] | **[total]** | Documented / Tested |
| [Tool B] | [x] | [x] | [x] | [x] | [x] | **[total]** | Documented / Tested |

Column values are the weighted points (rating ÷ 5 × weight), so the row sums to the total.

Job scored: [one line naming the job]. Ratings reviewed on [date]; sources are linked in the article's methodology section.

## Moving older articles over

Articles published with a 5-point AHW Score or a Decision-Fit Score keep that score until their next substantive refresh. At that refresh, rescore them from the evidence under W.O.R.T.H. Don't convert old numbers mechanically: the dimensions are not one-to-one, so a converted number would claim a check that never happened.

## What the score does not do

- It does not measure output quality directly unless the evidence basis is *Tested*.
- It does not replace the "who should use / avoid" section. Two tools with the same total can suit very different readers.
- It is a snapshot. Pricing, features and policies change, so every score carries its review date.
