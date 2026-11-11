# Group L: Dataset B Challenge Submissions (H1N1 Vaccine Allocation)

Group L's five submission files for the Dataset B challenge. Each file decides, for every one of the 4,749 test respondents, whether the town should post them an H1N1 vaccine (`1.0`) or not (`0.0`). The goal is to minimise the town's total spending on **vaccines ($500 each)** and **hospitalisations ($25,000 each)**.

## Submissions

| Order | File | Model | Doses sent | Expected town cost | Cost per person |
|---|---|---|---|---|---|
| 1 | `challange_submission_group_L_order_1.csv` | Ensemble (logistic regression + gradient boosting) | 701 (14.8%) | $1,892,070 | $398.41 |
| 2 | `challange_submission_group_L_order_2.csv` | XGBoost | 671 (14.1%) | $1,892,348 | $398.47 |
| 3 | `challange_submission_group_L_order_3.csv` | HistGradientBoosting | 716 (15.1%) | $1,893,486 | $398.71 |
| 4 | `challange_submission_group_L_order_4.csv` | Tuned gradient boosting | 665 (14.0%) | $1,893,261 | $398.67 |
| 5 | `challange_submission_group_L_order_5.csv` | Random forest (isotonic-calibrated) | 780 (16.4%) | $1,893,129 | $398.64 |

Expected costs are for the 4,749 test respondents under the group's hospitalisation assumptions (see below). For comparison, sending no vaccines at all would cost an expected $1,954,095.

**Format:** two columns, `respondent_id` and `h1n1_vaccine`; 4,749 rows (IDs 4757–9505, in the same order as the test set); values 0.0 or 1.0; no missing values.

## Method

1. **Acceptance model:** each model predicts the probability that a person accepts the vaccine, trained on the 4,756 labelled respondents (attitudes, behaviour, health status and demographics). Missing answers are kept as indicator features, because they are informative (e.g. respondents who skipped the health-insurance question vaccinated at 24% vs 50%).
2. **Hospitalisation risk:**

   `P(hospitalised) = base × age multiplier × (1 + 0.5 × chronic conditions) × (1 − 0.6 if vaccinated)`

   The parameters are the median of estimates from three generative AIs (Claude, ChatGPT and Gemini): **0.5% baseline risk**, age multipliers from 0.8 (18–34) to 2.0 (65+), and **60% vaccine effectiveness**.
3. **Decision rule:** a dose is sent when the expected hospital saving outweighs the risk of wasting it:

   `P(accept) > 500 / (500 + 25,000 × hospitalisation risk × 0.6)`

   Each person gets their own threshold, so older people and those with chronic conditions are prioritised.
4. **Cost rules:** a sent dose costs $500; a person who wanted a vaccine but wasn't sent one is paid $500 in cash and stays unprotected; hospitalisation costs $25,000 × probability.

## How the order was chosen

About 30 candidate files from four group members were compared on the same people under the same cost rules. These five had the lowest expected cost, and they are within $1,500 of each other (about 0.3 dollars per person on ~$398), so they perform equivalently. The order reflects the group's judgement:

- **Ensemble** first: lowest expected test cost and lowest hold-out cost.
- **XGBoost** second: next-lowest expected test cost.
- **HistGradientBoosting** third: lowest cross-validated cost in the group's validation ($417.61 per person).
- **Tuned gradient boosting** and **calibrated random forest** complete the set, giving five different model types.

## Files deliberately not submitted

- **Versions built with a 6% hospitalisation placeholder** (about 2,200–2,850 doses). Under the evidence-based 0.5% assumption, they would cost more than sending no vaccines at all.
- **Very cautious versions** (305–421 doses), which cost $12,000–21,000 more than these five.
- **Files containing probabilities** instead of 0/1 decisions.

## Assumptions and limitations

- People paid $500 in cash instead of a dose are assumed to stay **unvaccinated**.
- Hospitalisation parameters are AI-derived estimates, not learned from data. The AIs disagreed (0.5% vs 1.5% baseline); the CDC estimate for 2009 H1N1 is about 0.45% of cases hospitalised.
- Expected costs are estimates. The real cost depends on who actually accepts and on the assessors' hospitalisation assumptions. If the true risk is higher (e.g. 1.5%), these files still beat sending no vaccines but would send fewer doses than ideal.

## Group L

| Name | Contribution |
|---|---|
| Israt | Notebook (EDA, tuned models, cost analysis), XGBoost and random forest submissions, final comparison |
| | |
| | |
