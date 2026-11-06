# Data

`meridian_churn_data.csv` — 1,400 synthetic B2B SaaS accounts, 14 columns.

| Column | Type | Notes |
|---|---|---|
| `customer_id` | string | unique, no duplicates |
| `segment` | categorical | SMB / Enterprise |
| `plan_tier` | categorical | Basic / Pro / Enterprise |
| `monthly_price` | float | 3 sign-flipped values corrected in cleaning |
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

Derived in cleaning: `nps_responded` (bool), `seat_utilization` (`num_users_active / num_users_licensed`).

Meridian Cloud is a fictional company; this data is synthetic and carries no confidentiality restriction.
