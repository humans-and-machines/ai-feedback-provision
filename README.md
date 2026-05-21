# AI Assistance for Discretionary Work: Increasing Feedback Provision in Higher Education

This repository contains the analysis code, anonymized dataset, and pre-computed results for a randomized field experiment studying whether AI-assisted feedback drafting increases feedback provision by teaching assistants (TAs) in a large undergraduate course.

## Repository structure

```
.
├── dataset.csv          # Anonymized student–question–TA observations
├── analysis.ipynb       # Regressions and descriptive statistics
├── plots.ipynb          # Figures from homework-level summaries
├── tables/              # CSV outputs (means, SEs, 95% CIs)
└── figures/             # PDF figure ouputs
```

## Dataset

`dataset.csv` contains **2,828** observations (one row per student submission graded by a TA). Identifiers are anonymized (`student_id`, `ta_id`, `question_id`).

| Column | Description |
|--------|-------------|
| `hw_id` | Homework assignment (1–4) |
| `question_id` | Question identifier |
| `student_id` | Anonymized student |
| `ta_id` | Anonymized TA |
| `ta_role` | TA role (`GTA` or `UTA`) |
| `question_type` | Question category (`applied` or `conceptual`) |
| `has_mistakes` | Whether the submission contained mistakes (0/1) |
| `treatment` | Randomized assignment: `0` = control, `1` = treatment |
| `feedback_provision` | Whether written feedback was provided (0/1) |
| `feedback_time` | Time spent writing feedback in seconds/character; defined only when feedback was given |
| `feedback_length` | Feedback length in characters; defined only when feedback was given |
| `feedback_rating` | Student usefulness rating of feedback (1–5); defined only when the student rated received feedback |
| `draft_usage` | Share of AI draft used in final feedback (treatment only); defined only when feedback was given |
| `draft_rating` | TA usefulness rating of the AI draft (treatment only) |
| `similarity_score` | ROUGE-L F1 between draft and final feedback (treatment only); defined only when feedback was given |
| `grade_reconsideration` | Whether the TA would consider changing the grade after seeing the draft (`keep`, `increase`, `decrease`; treatment only) |

Missing values (`NaN`) indicate that an outcome was not defined for that observation (e.g., feedback_rating is only defined when students rated received feedback).

## Analysis workflow

### 1. Statistical analysis (`analysis.ipynb`)

Run all cells from the repository root. The notebook:

1. **Loads** `dataset.csv` and reports sample sizes by outcome and condition.
2. **Runs OLS regressions** with question fixed effects and cluster-robust standard errors (clustered by `student_id`) for outcomes observed in both conditions:
   - `feedback_provision`
   - `feedback_length`
   - `feedback_time`
   - `feedback_rating`
3. **Computes descriptive statistics** (mean, SE, 95% CI, *n*) and writes CSVs to `tables/`:
   - **Shared outcomes** (control vs. treatment): course-level (`shared_results.csv`), homework-level (`shared_by_hw.csv`), and stratified by `ta_role`, `question_type`, or `has_mistakes` (`shared_stratified_*.csv`).
   - **Treatment-only outcomes**: course-level (`treatment_results.csv`), homework-level (`treatment_by_hw.csv`), and stratified (`treatment_stratified_*.csv`).

To regenerate stratified tables for `question_type` or `has_mistakes`, set `strat_col` in the stratified cells of `analysis.ipynb` and re-run those cells (the committed CSVs include all three stratifications).

### 2. Figures (`plots.ipynb`)

Reads homework-level summaries from `tables/shared_by_hw.csv` and `tables/treatment_by_hw.csv`, then saves `figures/temporal_trends.pdf`. Run after `analysis.ipynb` (or use the committed table files).

## Setup

Requires **Python 3.10+**.

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook
```

Open `analysis.ipynb`, then `plots.ipynb`, and run all cells.

## Outputs

| Path | Contents |
|------|----------|
| `tables/shared_results.csv` | Course-level means for shared outcomes |
| `tables/shared_by_hw.csv` | Homework-level shared outcomes |
| `tables/shared_stratified_*.csv` | Shared outcomes by `ta_role`, `question_type`, or `has_mistakes` |
| `tables/treatment_results.csv` | Course-level treatment-only outcomes |
| `tables/treatment_by_hw.csv` | Homework-level treatment-only outcomes |
| `tables/treatment_stratified_*.csv` | Treatment-only outcomes by stratification variable |
| `figures/temporal_trends.pdf` | Temporal trends figure |
