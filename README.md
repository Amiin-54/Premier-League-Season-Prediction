# Premier League Match and Season Prediction

Predicting Premier League matches and the 2025/26 final table from preseason data, comparing logistic regression, Poisson and Bayesian Poisson models.

MSc Data Analytics dissertation, Queen Mary University of London (2025/26).

## Overview
Can information available *before* a season starts predict match results, goals and the final league table? This project builds 16 preseason predictors, trains the models on historical seasons and tests them on the unseen 2025/26 season. Each model then simulates the full season 10,000 times to produce expected points, finishing-position ranges and title, top-four and relegation probabilities.

## Data
- Source: [Football-Data](https://football-data.co.uk/englandm.php), 6,460 Premier League matches from 2009/10 to 2025/26
- Chronological split: training 2010/11 to 2023/24 (5,320 matches), validation 2024/25 (380), final test 2025/26 (380)
- Final models were refitted on training plus validation data (5,700 matches) before testing
- The CSV files are not included in this repo; download them from Football-Data

## Method
- **Features:** previous-season points, goals scored and conceded, shots on target for and against (all per game), league position, preseason Elo rating and a promoted-team flag, for both the home and away team (16 predictors). Everything is built from information available before the season starts, to avoid data leakage.
- **Models:** majority-class baseline; logistic regression (chosen over gradient boosting on validation log loss); standard Poisson regression (separate home and away goal models); Bayesian Poisson regression (PyMC, NUTS sampler, 4 chains, 4,000 posterior draws) with credible intervals for expected goals.
- **Evaluation:** accuracy, macro F1 and log loss for match results; MAE and Poisson deviance for goals; position MAE, points MAE and Spearman correlation for the league table.

## Results (2025/26 test season)

| Model | Accuracy | Macro F1 | Log loss |
|---|---|---|---|
| Majority-class baseline | 0.426 | 0.199 | 1.082 |
| Logistic regression | 0.468 | 0.351 | 1.039 |
| Standard Poisson | 0.474 | 0.352 | 1.052 |
| Bayesian Poisson | 0.474 | 0.354 | 1.038 |

| Model | Position MAE | Points MAE | Spearman correlation |
|---|---|---|---|
| Logistic regression | 3.90 | 9.30 | 0.657 |
| Standard Poisson | 3.70 | 10.16 | 0.633 |
| Bayesian Poisson | 3.70 | 9.16 | 0.675 |

## Key findings
- All three models beat the baseline, but the differences between them were small.
- The Bayesian model had the best log loss, macro F1, points MAE and Spearman correlation. Its main benefit was quantifying uncertainty (credible intervals) rather than a large gain in accuracy.
- Sampling diagnostics were clean: max R-hat of 1.004 and no divergent transitions.
- No model ever predicted a draw as the most likely result (104 draws in the test season).
- Preseason data captures broad team strength but not in-season change. No model picked Arsenal as champion, and Sunderland, predicted 18th, finished 7th.

## Limitations and future work
- Predictors are fixed at the start of the season, so the models cannot react to form, injuries or transfers
- Home and away goals are assumed conditionally independent (no Dixon-Coles adjustment)
- The Bayesian model is feature-based rather than hierarchical
- Model selection and testing each used a single season

Possible extensions: in-season updating of team strength, a hierarchical Bayesian model and rolling validation across several seasons.

## Repository contents
- `premier_league_prediction.ipynb`: full analysis (data preparation, EDA, feature engineering, models, simulations)
- `dissertation_report.pdf`: full dissertation report

## How to run
1. Download the season CSVs from Football-Data and update the file paths in the notebook's data-loading cell.
2. Install the libraries: `pip install pandas numpy scipy scikit-learn matplotlib seaborn pymc arviz`
3. Open the notebook in Jupyter and run all cells.

## Tools
Python, pandas, NumPy, SciPy, scikit-learn, PyMC, ArviZ, matplotlib, seaborn

## Author
Mohamed-Amin Abdullahi, MSc Data Analytics, Queen Mary University of London
[add your LinkedIn link here]
