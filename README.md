# Cancer Death Rate Prediction — Linear Models Project

**Dataset:** 3,047 U.S. counties | 30 variables  
**Target:** `TARGET_deathRate` — mean per-capita cancer deaths per 100,000 residents

---

## Overview

This project builds a multiple linear regression model to predict county-level cancer death rates using socioeconomic and healthcare-related predictors. The analysis follows a structured 7-step methodology: exploratory analysis, initial modeling, automated variable selection, diagnostics, outlier assessment, transformation evaluation, and final inference.

---

## Repository Structure

```
├── DS64510_Project_Data.csv       # Source dataset
├── TravisDarby_Project.Rmd        # Full R Markdown source
├── TravisDarby_Project.pdf        # Rendered project report
└── README.md
```

---

## Data

| Property | Value |
|---|---|
| Rows | 3,047 U.S. counties |
| Columns | 30 (28 numeric, 2 character) |
| Target variable | `TARGET_deathRate` |
| Notable missing data | `PctSomeCol18_24` (2,285), `PctPrivateCoverageAlone` (609), `PctEmployed16_Over` (152) |

The target variable is approximately normally distributed, centered around 180–200 deaths per 100,000, ranging from ~50 to ~350.

> **Data quality note:** `MedianAge` contains implausible values exceeding 350 years, likely data entry errors. These were filtered for visualization but not removed from modeling.

---

## Methodology

### Step 1 — Exploratory Variable Selection

Eight candidate predictors were chosen based on scatter plots against `TARGET_deathRate`:

| Variable | Description | Observed Relationship |
|---|---|---|
| `incidenceRate` | Cancer diagnoses per 100,000 | Strong positive |
| `medIncome` | Median county income | Moderate negative |
| `avgAnnCount` | Mean annual cancer cases reported | No clear trend |
| `MedianAge` | Median age of county residents | No clear trend (data quality issues) |
| `studyPerCap` | Clinical trial participation per capita | No clear trend |
| `PctHS25_Over` | % residents 25+ with only HS diploma | Selected as education proxy |
| `PctPrivateCoverage` | % with private health insurance | Selected as insurance proxy |
| `PctPublicCoverage` | % with public health insurance | Selected as public coverage proxy |

---

### Step 2 — Initial Model

All eight predictors were entered into a linear model.

**R² = 0.4623** — approximately 46% of variance explained.

Five of eight predictors were statistically significant at α = 0.05:

- `incidenceRate` *(strongest predictor)*
- `medIncome`
- `PctHS25_Over`
- `PctPrivateCoverage`
- `avgAnnCount`

`MedianAge`, `studyPerCap`, and `PctPublicCoverage` were not significant. Notably, `studyPerCap` — hypothesized to be an important predictor — had the highest p-value (0.91).

---

### Step 3 — Automated Variable Selection

Two procedures were applied to the initial 8-variable model:

**`fastbw()` (p-value criterion, α = 0.05)**
Removed `MedianAge`, `studyPerCap`, and `PctPublicCoverage`. Adjusted R² = 0.4609 — identical to the full model, confirming the removed variables added no meaningful explanatory power.

**`stepAIC()` (AIC-based stepwise)**
Removed `MedianAge` and `studyPerCap` but retained `PctPublicCoverage` (p = 0.15). Despite the marginal AIC reduction, this variable remained non-significant.

✅ **Selected model:** `fastbw()` result — 5 predictors, cleaner and fully significant.

---

### Step 4 — Diagnostics

| Test | Result | Conclusion |
|---|---|---|
| Breusch-Pagan (BP = 70.31) | p < 0.001 | Suggests heteroscedasticity |
| Shapiro-Wilk (W = 0.9837) | p < 0.001 | Suggests non-normal residuals |
| Durbin-Watson (DW = 1.68) | p < 0.001 | Suggests autocorrelation |

However, **visual diagnostics told a more reassuring story:**
- Fitted vs. residuals plot: circular, unstructured pattern — no systematic issues
- Q-Q plot: no gross deviations from normality
- Lagged residual plot: no serial autocorrelation patterns

Given n = 3,047, the formal tests are highly sensitive to minor deviations that may not be practically meaningful.

---

### Step 5 — Outliers & Influential Observations

- **14 outliers** identified via standardized residuals (> ±3), representing 0.46% of the data
- Maximum standardized residual: **6.90**
- Maximum Cook's Distance: **0.266** — well below the threshold of 1.0
- No observations exceeded the 50th percentile of the F-distribution

**Conclusion:** Outliers exist but do not meaningfully influence the model.

---

### Step 6 — Box-Cox Transformation

Box-Cox analysis suggested λ = **0.7879** (slightly below 1.0). A transformed model was fit using `TARGET_deathRate^0.7879`. The diagnostic plot showed no improvement over the untransformed model.

**Conclusion:** No transformation was applied. The original model already meets the practical assumptions of linear regression.

---

### Step 7 — Final Model

```
TARGET_deathRate = 105.63
                  − 0.00085 · avgAnnCount
                  + 0.2334  · incidenceRate
                  − 0.000170 · medIncome
                  + 0.9010  · PctHS25_Over
                  − 0.8455  · PctPrivateCoverage
```

#### Parameter Estimates

| Predictor | Estimate | Std. Error | t-value | p-value |
|---|---|---|---|---|
| (Intercept) | 105.63 | 4.262 | 24.786 | < 0.001 |
| `avgAnnCount` | −0.00085 | 0.000280 | −3.039 | 0.002 |
| `incidenceRate` | 0.2334 | 0.006939 | 33.634 | < 0.001 |
| `medIncome` | −0.000170 | 0.0000508 | −3.356 | < 0.001 |
| `PctHS25_Over` | 0.9010 | 0.06254 | 14.407 | < 0.001 |
| `PctPrivateCoverage` | −0.8455 | 0.05185 | −16.308 | < 0.001 |

**R² = 0.4618 | Adjusted R² = 0.4609 | Residual Std. Error = 20.38**

#### Confidence Interval — `PctHS25_Over` slope
> 95% CI: **[−0.778, 1.024]**

#### Confidence Interval — Prediction at Median Predictor Values
*(avgAnnCount = 171, incidenceRate = 453.55, medIncome = $45,207, PctHS25_Over = 35.3%, PctPrivateCoverage = 65.1%)*
> 95% CI for mean response: **[179.61, 181.18]**

#### Prediction Interval — Specific County
*(avgAnnCount = 155, incidenceRate = 467.1, medIncome = $39,303, PctHS25_Over = 39.8%, PctPrivateCoverage = 59.8%)*
> 95% Prediction Interval: **[153.15, 233.08]**

---

## Key Takeaways

- **`incidenceRate`** is the dominant predictor — higher diagnosis rates strongly predict higher death rates.
- **`PctPrivateCoverage`** has the strongest negative association — counties with more private insurance tend to have lower death rates, suggesting healthcare access matters significantly.
- **`PctHS25_Over`** is positively associated with death rate — counties where more residents stopped at a high school diploma tend to fare worse, pointing to education as a health equity factor.
- **`medIncome`** is negatively associated — higher-income counties have lower death rates, consistent with broader socioeconomic health disparities.
- The model explains ~46% of variance, leaving room for additional predictors (e.g., race/ethnicity, smoking rates, rural/urban classification) that could improve performance.

---
