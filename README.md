# What's Actually Driving Customer Churn

A churn analysis for a B2B SaaS business (Meridian Cloud, a fictional company) where
leadership had already decided on the answer. Churn had risen for two quarters, a price
increase had landed in the same window, and the proposal on the table was to roll the price
increase back.

The data says the price increase is not the cause, and rolling it back would cost revenue
without fixing churn.

## The finding

Taken alone, the price increase looks damning — customers who got one churn at **1.71x the
odds** of those who didn't (χ² p < 0.001, Fisher's exact p < 0.001). That is the correlation
leadership was reacting to, and it is real.

It does not survive a control for product usage:

| Model | Price increase coefficient | Odds ratio | p-value |
|---|---|---|---|
| Price increase only | 0.538 | 1.71 | **0.000003** |
| + support tickets | 0.482 | 1.62 | **0.00005** |
| + product usage score | 0.102 | 1.11 | 0.445 |
| + NPS | 0.085 | 1.09 | 0.596 |
| + seat utilization | 0.084 | 1.09 | 0.599 |
| + support response time | 0.088 | 1.09 | 0.585 |

Adding one variable — product usage score — collapses the price effect from an odds ratio of
1.62 to 1.11 and from p < 0.0001 to p = 0.45. The accounts that churned after the price
increase were already disengaged. Price was the trigger, not the cause.

In the full model, the variables that survive are **product usage score** (coef −0.055,
p < 0.001) and **support tickets in the last 90 days** (coef +0.128, p = 0.002). Price
increase, NPS, seat utilization, active users and support response time all fall away.

Monthly price itself never mattered at all — a one-sided t-test of price between churned and
retained accounts gives p = 0.17.

## Method

1. **Profile and clean** (`01_data_cleaning.ipynb`) — 1,400 accounts, 14 columns. Three
   negative `monthly_price` values, each within a few percent of its plan tier's median in
   absolute terms, so sign-flips rather than genuine refunds; corrected rather than dropped.
   27.1% of `nps_score` is missing, so non-response is flagged as its own feature
   (`nps_responded`) instead of being imputed. Post-clean consistency checks on seat counts,
   tenure, score ranges.
2. **Screen every feature** (`02_churn_driver_analysis.ipynb`) — chi-square for categorical,
   Welch's t-test for continuous. Seven features clear p < 0.05; seven don't.
3. **Build up the model one variable at a time** — the point isn't the final model, it's
   watching *when* the price coefficient dies and what kills it.
4. **Test pairwise interactions** across the seven significant features. Only one of 28 terms
   reaches p < 0.05 (price increase × NPS, p = 0.045) — which at 28 tests is about what you'd
   expect from chance alone, so it isn't treated as a finding.

## Caveats worth knowing before you read the deck

- **`nps_score` is 27% missing, and statsmodels drops those rows.** Any model including NPS
  runs on 1,020 accounts rather than 1,400. The central result doesn't depend on it: the
  stepwise run that kills the price effect by adding `product_usage_score` does so at the
  full n = 1,400.
- **This is observational.** Usage is measured before churn, which rules out the most obvious
  reverse-causation story, but nothing here establishes that raising usage *causes* retention.
  Slide 10 of the deck says so too.
- **Several figures in the deck aren't computed in these notebooks** — the usage-quartile 3x
  ratio, the 52.9% vs 33.3% median seat utilization, the per-segment churn rates on slide 8.
  They're consistent with what the notebooks show, but the code behind them isn't here.
- **The stored cell outputs predate a correctness fix.** An earlier version of
  `02_churn_driver_analysis.ipynb` reloaded the raw CSV partway through, which silently undid
  the price sign correction for the cells after it. The code is fixed; the saved outputs below
  those cells were produced before the fix, so the monthly-price figures will shift slightly
  on a re-run. The conclusion does not change — price is not significant either way.
- **`segment` and `plan_tier` are not significant on their own** (p = 0.62 and p = 0.68).
  Slide 8's claim is that the usage-churn relationship holds *within* segments, which is a
  different claim from segment predicting churn.

## Recommendation delivered

Don't roll back the price increase. Route accounts with usage scores under 50 and seat
activation under 50% to a retention team ahead of renewal, invest in onboarding and adoption,
and track usage and activation monthly.

## Repository contents

```
notebooks/01_data_cleaning.ipynb          profiling, sign-flip correction, non-response flag
notebooks/02_churn_driver_analysis.ipynb  significance screen, stepwise logit, interactions
deck/meridian_cloud_churn_findings.pptx   10-slide executive presentation
data/meridian_churn_data_cleaned.csv      post-cleaning extract, 1,400 accounts
```

Requires `pandas`, `numpy`, `scipy`, `statsmodels`, `matplotlib`, `seaborn`.

**On running these:** both notebooks begin by loading the *raw* 14-column extract
(`data/meridian_churn_data.csv`) and doing the cleaning themselves, and that raw file isn't
in the repo — so neither notebook runs end to end as-is. What is included is the cleaned
output, which has everything the regressions need: point the load cell at
`meridian_churn_data_cleaned.csv` and skip cleaning steps 1–7. See `data/README.md`.

Meridian Cloud is fictional and the dataset is synthetic.

## Credits

The analysis in this repository — the cleaning and validation, the significance screen, and
the logistic regression work in `notebooks/` — is my own code.

The project was coursework carried out with a team, and the executive deck in `deck/` was a
team deliverable.

Varnika Bethireddy
