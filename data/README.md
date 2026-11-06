# Data

Meridian Cloud is a fictional company; this data is synthetic and carries no confidentiality
restriction.

## What's here

**`meridian_churn_data_cleaned.csv`** — 1,400 accounts, 16 columns. This is the **output** of
`notebooks/01_data_cleaning.ipynb`, not its input: the three sign-flipped prices are already
corrected, and the two derived columns are already present.

## What's missing

**`meridian_churn_data.csv`** — the raw 14-column extract. Both notebooks start by loading it
and performing the cleaning themselves, so **neither notebook runs end to end without it.**

If you only want to reproduce the modeling, the cleaned file above has everything the
regressions need; point the load cell at it and skip cleaning steps 1–7.

## Schema

The raw file has these 14 columns:

| Column | Type | Notes |
|---|---|---|
| `customer_id` | string | unique, no duplicates |
| `segment` | categorical | SMB / Enterprise |
| `plan_tier` | categorical | Basic / Pro / Enterprise |
| `monthly_price` | float | 3 sign-flipped values in the raw file |
| `tenure_months` | int | |
| `signup_channel` | categorical | Organic / Partner / Paid Search / Outbound Sales |
| `support_tickets_last_90d` | int | |
| `avg_support_response_hours` | float | |
| `product_usage_score` | float | 0–100 |
| `num_users_licensed` | int | |
| `num_users_active` | int | |
| `had_price_increase_last_quarter` | bool | |
| `nps_score` | float | 27.1% missing (380 of 1,400) |
| `churned` | bool | target |

The cleaned file adds two more:

| Column | Type | Derivation |
|---|---|---|
| `nps_responded` | bool | `nps_score.notna()` — non-response kept as signal rather than imputed |
| `seat_utilization` | float | `num_users_active / num_users_licensed` |

Cleaning applied: `monthly_price` taken as absolute value (three negatives, each within a few
percent of its plan tier's median, so sign-flips rather than refunds). `nps_score` left
missing rather than imputed. Post-clean range: 44.13 to 573.70.
