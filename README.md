# Dataset B: Final Five Submissions (H1N1 Vaccine Allocation)

These are the team's five final submission files for the H1N1 vaccine task. Each file decides, for every one of the 4,749 test respondents, whether the town should post them a vaccine (`1.0`) or not (`0.0`). The goal is to minimise the town's total spending on **vaccines ($500 each)** and **hospitalisations ($25,000 each)**.

## The five submissions

| Rank | File | Model | Doses sent | Expected town cost | Extra cost vs best possible |
|---|---|---|---|---|---|
| 1 | `best1_Ensemble.csv` | Ensemble (logistic regression + gradient boosting) | 701 (14.8%) | $1,892,070 | +$1,825 |
| 2 | `best2_XGBoost.csv` | XGBoost | 671 (14.1%) | $1,892,348 | +$2,103 |
| 3 | `best3_RandomForest_calibrated.csv` | Random forest (isotonic-calibrated) | 780 (16.4%) | $1,893,129 | +$2,884 |
| 4 | `best4_TunedGradientBoosting.csv` | Tuned gradient boosting | 665 (14.0%) | $1,893,261 | +$3,016 |
| 5 | `best5_HistGradientBoosting.csv` | HistGradientBoosting | 716 (15.1%) | $1,893,486 | +$3,241 |

`best5_model_ranking.csv` contains the same table in CSV form.

**Format:** each file has two columns, `respondent_id` and `h1n1_vaccine`, with 4,749 rows (IDs 4757–9505, in the same order as the test set) and values 0.0 or 1.0.

## How the files were made

1. **Acceptance model:** each model predicts the probability that a person accepts the vaccine, trained on the 4,756 labelled respondents (survey answers on attitudes, behaviour, health and demographics).
2. **Hospitalisation risk:** `base risk × age multiplier × (1 + 0.5 × chronic conditions)`, reduced by vaccine effectiveness if the person is actually vaccinated. The parameters are the median of the estimates from three generative AIs (Claude, ChatGPT and Gemini): **0.5% baseline risk** and **60% vaccine effectiveness**.
3. **Decision rule:** a dose is sent when

   `P(accept) > 500 / (500 + 25,000 × hospitalisation risk × effectiveness)`

   so each person has their own threshold, and higher-risk people are prioritised.

## How the five were chosen

Around 30 candidate files from four team members were compared. The test set has no labels, so each file was scored by **expected town cost**:

- **Who accepts:** the average of three independently trained models (logistic regression, calibrated random forest, HistGradientBoosting), none of which produced any submitted file. This avoids favouring one team member's model.
- **Cost rules:** a sent dose costs $500; a person who wanted a vaccine but wasn't sent one is paid $500 in cash and stays unprotected; hospitalisation costs $25,000 × probability.
- **Scenarios:** every file was scored at 0.5% (AI median), 1.5% (Gemini) and 6% baseline risk.

The five files above had the lowest expected cost under the evidence-based 0.5% assumption, and stayed in the top group whichever reference model was used. They are within about $3,000 (0.15%) of each other, so they are effectively equally good. One file per model type was kept.

## Files deliberately not submitted

- **Versions built with a 6% hospitalisation placeholder** (about 2,200–2,850 doses). Under the evidence-based 0.5% assumption they cost $2.15–2.33M, **more than sending no vaccines at all** ($1.95M).
- **Very cautious versions** (305–421 doses). These cost $12,000–21,000 more than the best files.
- **Files containing probabilities instead of 0/1 decisions.** They don't say who receives a dose.

## Assumptions and limitations

- A person paid $500 in cash instead of a dose is assumed to stay **unvaccinated**.
- Hospitalisation parameters are AI-derived estimates, not learned from data. The AIs disagreed (0.5% vs 1.5% baseline); the CDC figure for 2009 H1N1 is about 0.45% of cases hospitalised.
- Expected costs are estimates; the real cost depends on who actually accepts and on the assessors' own hospitalisation assumptions.
- If the true risk is higher (about 1.5%), all five files would still beat sending no vaccines, but would send fewer doses than is ideal.

## Team

| Name | Contribution |
|---|---|
| Israt | Notebook (EDA, tuned models, cost analysis), XGBoost and random forest submissions, final comparison |
| | Ensemble, tuned gradient boosting and HistGradientBoosting submissions |
| | |
