# Role-Aware Dream XI Selection (T20)

## Overview
A role-aware, data-driven framework for selecting a balanced Dream XI in T20 cricket. The approach scores players relative to their roles, separates skill from experience, and emphasizes team balance rather than individual extremes.

## Problem
Many ranking systems treat players as directly comparable and rely on single metrics, which overlooks role-specific objectives. In T20, roles (openers, middle-order, finishers, bowlers, all-rounders) have distinct responsibilities; selection should prioritize coverage and complementarity.

## Design principles
- Teams are systems; role interactions matter more than individual rankings.
- Metrics are meaningful only within a role.
- Experience is modeled separately from skill (as a confidence measure).
- The approach favors transparency over opaque models.

## Data
Primary data is under `dataset/`:
- `dataset/twb.csv` — batting statistics
- `dataset/twbo.csv` — bowling statistics
- `dataset/top t20 teams/` — team-level files used for role analysis
- Outputs: `dataset/eda_plots/`, `dataset/eda_slide_imgs/`

Note: the dataset emphasizes historically successful teams by design (intentional survivorship bias to learn what tends to work).

## Role segmentation
Players are segmented into role groups before scoring. Examples:
- Batters: openers, middle-order, finishers
- Bowlers: fast, medium pace, off spin, leg spin
- All-rounders: combined batting & bowling metrics
Players are compared only to peers in the same role.

## Normalization and direction handling
Metrics are standardized (z-score) within each role group:
```
z = (value - role_mean) / role_std
```
Metrics where lower is better (e.g., economy rate, bowling SR) are inverted so that higher z-scores always indicate better performance.

## Data-driven weights
Weights for each metric are derived from variance across elite-role data:
```
weight_i = variance_i / sum(variances)
```
This highlights metrics that differentiate elite performers and is applied per role.

## Scoring
Skill score (per role):
```
Skill_Score = sum(weight_i * z_i)
```
Experience (innings factor) is modeled as `log(1 + innings)`, normalized so that the mean is 1. Final score:
```
Final_Score = Skill_Score * Innings_Factor
```
This avoids seniority bias while accounting for proven performance.

### All-rounders
Combine batting and bowling skill scores. Experience uses a balanced factor combining both batting and bowling innings.

### Captain selection (optional)
A separate score that emphasizes experience:
```
Captain_Score = 0.25 * Skill + 0.75 * Experience
```

## Team construction
Typical XI template used by the pipeline:
- 2 openers
- 2 middle-order batters
- 2 finishers
- 2 all-rounders
- 3–4 bowlers (balanced by type)
The model ensures role coverage before maximizing scores.

## Outputs
- Ranked lists per role with Skill, Experience, and Final_Score
- Visualizations and CSV exports
- Slide-ready images in `dataset/eda_slide_imgs/`

## Strengths and limitations
Strengths:
- Role-aware and interpretable
- Data-driven weighting
- Robust to outliers

Limitations:
- Survivorship bias in training data
- Role labels may require manual verification
- No match-level context (pitch, opposition, venue)

## How to run (quick)
1. Set up environment and install dependencies:
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```
2. Open `eda.ipynb` for the concise EDA and slide-ready figures.
3. Slide images are saved to `dataset/eda_slide_imgs/`.

## Files of interest
- `eda.ipynb` — notebook with concise EDA and slide-ready plots
- `dataset/twb.csv`, `dataset/twbo.csv` — core data
- `dataset/eda_plots/`, `dataset/eda_slide_imgs/` — visual artifacts

---

If you'd like, I can add a `requirements.txt` and a small example script that runs the scoring pipeline, or create a one-slide PPTX with the saved images and a short bullet summary.

