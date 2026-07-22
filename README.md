[🇨🇳 中文](README_CN.md)

# Vehicle Competitive Intelligence & Pricing Analytics 🚗

**Turning vehicle specs into pricing insights — an automated competitive analysis framework.**

Traditional competitive analysis relies on consultants manually building Excel benchmarks — time-consuming and hard to quantify. This project builds an automated framework: input vehicle specs, automatically identify competitors, quantify feature premiums, and output pricing conclusions.

> ⚠️ The project did not achieve its original business goals due to insufficient data. However, the analysis framework proved reusable and the methodology offers insights for small-sample ML scenarios.

---

## Key Decisions

| Phase | Decision / Event | Rationale |
| :--- | :--- | :--- |
| Data Acquisition | Web scraping failed, so decided to use the available 243 records / 80+ brands. Concerned whether pricing analysis is feasible with such limited data. | Relled on AI advice: "Random Forest is not sensitive to 200 samples." Instinct said the sample was too small, but the project launched without independent verification. |
| Missing Value Handling | Data had many missing values; dropping them would make the sample even sparser, so chose to impute them. | Observed that severely missing columns often had highly correlated features for cross-reference. Example: wheelbase and length had staggered missing values. Based on automotive design knowledge (wheelbase and length are highly linearly correlated), used linear regression to predict and fill each. |
| Data Cleaning | Original data format was extremely messy — values mixed strings and numbers, some cell values were ranges instead of exact numbers, etc. | Split strings from numbers; replaced range-formatted values with mean or median. |
| Modeling Diagnosis | Regression R² was abnormally low. Considering the EDA report, the issue was unlikely to be model choice or parameter settings. | Noted that the data was sparse with uneven distribution. Checked whether price distributions between training and test sets were consistent — found a 60k RMB mean difference → applied StratifiedShuffleSplit to calibrate. After checking the classification model, realized price brackets almost perfectly overlap with vehicle segment — the model learned "size = price", not pricing logic. Continuing to tune features or models was pointless. Better to systematically review and strengthen feasibility assessment before formally launching an ML project. |
| Systematic Review | Can small-sample ML projects succeed at all? | Summarized 3 inherently friendly scenarios (strong physical laws, pre-trained priors, explicit rules) and produced a reusable feasibility judgment framework. For this project to succeed: thousands of valid records, relatively balanced brand distribution, and paired same-brand same-segment single-diff samples. |

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
├── notebooks/                   # Numbered by execution order
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
