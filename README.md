# ⚽ World Cup Match Outcome Predictor

> **Status: Research / Experimental** — This project is under active development. Results and methodology may change.

A gradient boosting model that predicts the probability of match outcomes (win / draw / loss) for FIFA World Cup fixtures.

---

## Overview

This project applies gradient boosting classification (e.g. XGBoost / LightGBM / scikit-learn GBM) to historical World Cup and international football data to estimate the probability of each possible match outcome. The goal is to explore how well structured tabular features — team form, squad strength, historical head-to-head records — can predict tournament results.

---

## Data Sources & Features

### Data Sources

| Source | Description |
|--------|-------------|
| FIFA World Cup match history | Results from all World Cup editions |
| International match results | Broader international fixtures for form features |
| FIFA / ELO ratings | Proxy for team strength at time of match |

### Feature Set

**Team strength**
- FIFA ranking & ELO rating at time of match
- Average goals scored / conceded over last N matches

**Form**
- Win rate over last 5 / 10 international matches
- Goals per game (attack & defence) in recent fixtures
- Unbeaten streak leading into the tournament

**Match context**
- Stage of tournament (group stage, knockouts, final)
- Confederation (UEFA, CONMEBOL, etc.)
- Neutral venue flag

**Head-to-head**
- Historical win/draw/loss record between the two teams
- Average goals in previous meetings

> **Note:** Feature availability varies across World Cup editions. Earlier tournaments (pre-1990) have sparser data and are treated with care during training/evaluation splits.

---

## Model

| Detail | Value |
|--------|-------|
| Algorithm | Gradient Boosting Classifier |
| Output | Softmax probabilities: P(home win), P(draw), P(away win) |
| Evaluation strategy | Leave-one-tournament-out cross-validation |
| Baseline | Naive prior based on historical outcome frequencies |

### Hyperparameters (current best)

```python
{
    "n_estimators": 500,
    "max_depth": 4,
    "learning_rate": 0.05,
    "subsample": 0.8,
    "colsample_bytree": 0.8,
    "min_child_weight": 3
}
```

---

## Performance & Metrics

Evaluation is done using **leave-one-tournament-out** cross-validation — the model is trained on all tournaments except one, then tested on the held-out tournament. This simulates real prediction conditions.

### Key Metrics

| Metric | Score |
|--------|-------|
| Log Loss (avg across folds) | _TBD_ |
| Accuracy (3-class) | _TBD_ |
| Brier Score | _TBD_ |
| Calibration (reliability diagram) | _see `/notebooks`_ |

> Accuracy alone is a weak signal for probabilistic outcome prediction — log loss and Brier score are the primary evaluation metrics here.

### Benchmark comparison

| Model | Log Loss |
|-------|----------|
| Naive prior (baseline) | _TBD_ |
| Gradient Boost (this project) | _TBD_ |
| ELO-only model | _TBD_ |

_Metrics will be populated as experiments are completed._

---

## Project Structure

```
├── data/
│   ├── raw/              # Original data files
│   └── processed/        # Cleaned, feature-engineered datasets
├── notebooks/
│   ├── eda.ipynb         # Exploratory data analysis
│   ├── feature_engineering.ipynb
│   └── model_evaluation.ipynb
├── src/
│   ├── features.py       # Feature construction pipeline
│   ├── model.py          # Model training & serialisation
│   └── evaluate.py       # Cross-validation & metrics
├── models/               # Saved model artefacts
├── requirements.txt
└── README.md
```

---

## Getting Started

```bash
# Clone the repo
git clone https://github.com/your-username/worldcup-gbm.git
cd worldcup-gbm

# Install dependencies
pip install -r requirements.txt

# Run feature engineering
python src/features.py

# Train the model
python src/model.py

# Evaluate
python src/evaluate.py
```

---

## Limitations & Known Issues

- **Small dataset**: World Cup matches number in the hundreds, not thousands — overfitting is a real concern.
- **Squad composition**: Player-level data is not currently included; team-level proxies are used instead.
- **Temporal leakage risk**: Care is taken to prevent future data from leaking into training features, but this is an ongoing concern to audit.
- **Tournament dynamics**: Knockout-stage incentives and tactical rotation during group stages are hard to capture with current features.

---

## Roadmap

- [ ] Add player-level aggregate features (avg market value, avg caps)
- [ ] Incorporate expected goals (xG) where available
- [ ] Experiment with SHAP values for feature importance explainability
- [ ] Add a simple prediction CLI for upcoming fixtures
- [ ] Calibrate probabilities using Platt scaling or isotonic regression

---

## Contributing

This is a research project — feedback, issues, and PRs are welcome. If you spot a data quality issue or have a feature idea, open an issue.

---

## License

MIT
