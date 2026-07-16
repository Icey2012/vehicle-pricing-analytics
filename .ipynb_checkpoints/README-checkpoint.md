[🇨🇳 中文](README_CN.md)

# Vehicle Competitive Intelligence & Pricing Analytics 🚗

**A data-driven approach to automating vehicle competitive analysis.**

Traditional competitive analysis relies on consultants manually building Excel benchmarks — time-consuming and hard to quantify. This project builds an automated framework: input vehicle specs, automatically identify competitors, quantify feature premiums, and output pricing conclusions.

> ⚠️ The project did not achieve its original business goals due to insufficient data. However, the analysis framework proved reusable and the methodology offers insights for small-sample ML scenarios.

---

## Key Decisions

| Phase | Decision | Rationale |
| :--- | :--- | :--- |
| Kickoff | 243 records / 80+ brands — is pricing analysis feasible? | Initial instinct said sample is too small. AI suggested Random Forest handles 200 samples well. Decision proceeded without independent verification. |
| Data cleaning | How to fill missing wheelbase? | Automotive domain knowledge: wheelbase and length are highly correlated (>0.7). Used linear regression on length_mm to predict missing wheelbase — not mean imputation. |
| Feature engineering | How to encode 80+ brands? | Target encoding (reasonable attempt at the time). Post-mortem revealed target encoding is unstable under small samples — model over-relied on it as a shortcut. |
| Modeling diagnosis | Regression R² is low — where to start? | Checked train/test price distribution first (not hyperparameter tuning). Found a 60k RMB mean difference → applied StratifiedShuffleSplit. |
| Stop signal | When to stop tuning? | Observed that price brackets almost perfectly overlap with vehicle segment — model learned "size = price", not pricing logic. No point continuing. Shifted to systematic post-mortem. |
| Post-mortem | Can small-sample ML succeed? | Identified 3 inherently friendly scenarios (strong physical laws, pre-trained priors, explicit rules) and 6 remedial techniques. Output a reusable feasibility framework. For this project to succeed: 3000+ records, ≥50 per brand, pairwise same-brand same-segment single-diff samples. |

---

## Results Summary

Under **243 samples, 80+ brands** (fewer than 3 cars per brand on average):

- Low-price segment (<150k RMB): feature premiums quantifiable, MAE 1-3k
- High-price segment (>500k RMB): fewer than 5 samples, statistically meaningless
- Project archived, not restarting; framework code retained in portfolio

<details>
<summary>Detailed metrics (click to expand)</summary>

| Method | Metric | Note |
|------|------|------|
| Regression | R² ≈ 0.40, MAE ≈ 74k RMB | Data insufficient |
| Classification (6 price tiers) | Accuracy 63% | Retained as small-sample demo |
| Low-price segment (<150k) MAE | 1-3k RMB | Feature premiums quantifiable |
| High-price segment (>500k) MAE | 554k RMB | Only 3 samples |

</details>

---

## Key Takeaways

- Feasibility assessment must precede project kickoff — applies to any ML project. AI optimism must be independently validated.
- Budget cars and premium cars follow fundamentally different pricing logic. They should not be modeled together. Domain understanding matters more than hyperparameter tuning.

---

## Project Structure

```
.
├── Project Proposal.txt          # Project proposal (objectives, methodology, targets)
├── Project Proposal_CN           # Chinese version
├── EDA_Insight                   # Key EDA findings summary
├── post-mortem.ipynb             # Post-mortem (failure diagnosis + methodology)
├── post-mortem_CN.ipynb          # Chinese post-mortem
├── README.md                     # English README
├── README_CN.md                  # Chinese README
│
├── data/
│   ├── raw/                      # Raw data (4 cleaning stages + Kaggle supplement)
│   │   ├── 01_hand_made_original_data.xlsx
│   │   ├── 02_rough_machining.xlsx
│   │   ├── 03_hand_AI_cleaned_data.xlsx
│   │   ├── 04_cleaned_data.xlsx
│   │   ├── kaggle_original_data.csv
│   │   └── kaggle_engineered_data.csv
│   └── processed/                # Train/test data after feature engineering
│       ├── X_train_df.csv
│       ├── X_test_df.csv
│       ├── y_train_df.csv
│       ├── y_test_df.csv
│       ├── final_before_feng.csv
│       └── best_features.txt     # Feature importance selection results
│
├── notebooks/ (numbered by execution order)
│   ├── 00_download_kaggle_data.ipynb   # Kaggle data supplement
│   ├── 00_kaggle_EDA.ipynb             # Kaggle data exploration
│   ├── 01_raw_data_obsevation.ipynb    # Initial observation
│   ├── 02_raw_data_cleansing.ipynb     # Data cleaning (missing value, formatting)
│   ├── 03_EDA_insight.ipynb            # Exploratory data analysis
│   ├── 04_feature_engineering.ipynb    # Feature engineering
│   ├── 05_model_training_regression.ipynb  # Regression model training
│   ├── kaggle_data_report.html         # Kaggle data profiling report
│   └── vehicle_data_profile.html       # Vehicle data profiling report
```

---

## Related Documents
