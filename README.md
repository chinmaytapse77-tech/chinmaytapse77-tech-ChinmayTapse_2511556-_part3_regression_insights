# Regression-Based Business Insights & Model Interpretation
## Capstone Part 3

---

## 1. Business Problem Summary

A retail chain's leadership team wants to understand **what factors drive monthly sales performance across stores**. They are considering operational actions such as increasing marketing spend, improving inventory availability, changing discount strategy, reallocating staff, and prioritising certain store types or regions.

This analysis uses regression modelling to:
1. Identify which factors are most strongly associated with monthly sales
2. Quantify the size and statistical strength of each association
3. Surface variables that look important in isolation but turn out to be confounded
4. Translate findings into business recommendations (with appropriate caveats about causation)

---

## 2. Dataset Description

| Property | Value |
|---|---|
| File | `data/business_regression_data.xlsx` |
| Rows | 320 (80 stores × 4 monthly observations, Jan–Apr 2025) |
| Columns | 14 |
| Dependent variable | `monthly_sales` (continuous, range $400K–$947K) |
| Numerical predictors | marketing_spend, footfall, avg_discount_pct, staff_count, inventory_availability_pct, competitor_distance_km, holiday_flag, customer_rating |
| Categorical predictors | region (4), store_type (4) |
| Other columns (not used as predictors) | store_id, month, monthly_profit |

### Variables identified during exploration

**Dependent variable:** `monthly_sales`

**Numerical independent variables (8):**
- `marketing_spend` — monthly marketing budget ($)
- `footfall` — store visitors per month
- `avg_discount_pct` — average discount applied (0–29%)
- `staff_count` — number of employees
- `inventory_availability_pct` — share of SKUs in stock (71–99%)
- `competitor_distance_km` — distance to nearest competitor (km)
- `holiday_flag` — 1 if month contains a major holiday, else 0
- `customer_rating` — average customer rating (2.8–5.0)

**Categorical independent variables (2):**
- `region` (East, North, South, West)
- `store_type` (Airport, High Street, Mall, Residential)

**Variables NOT used as predictors:**
- `store_id` — identifier, no predictive value
- `month` — date already partially captured by `holiday_flag`
- `monthly_profit` — derived from `monthly_sales`, would create circular reasoning

**Variables that needed cleaning:**
- `competitor_distance_km` — 6 missing values, imputed with median (3.36 km)
- `customer_rating` — 8 missing values, imputed with median (3.9)

---

## 3. Regression Approach

1. **Data preparation:** impute missing values; verify no extreme outliers; confirm dependent variable approximately normal
2. **Dummy encoding:** convert region (4 levels → 3 dummies, East as reference) and store_type (4 levels → 3 dummies, Airport as reference)
3. **Simple regressions:** fit three single-predictor models on monthly_sales (footfall, marketing_spend, staff_count) to establish baselines
4. **Multiple regression:** fit one model with all 8 numerical predictors + 6 dummy variables (14 predictors total)
5. **Model comparison:** R², adjusted R², F-statistic, individual p-values
6. **Residual analysis:** identify top 5 over- and under-predicted records; look for systematic bias patterns

All regressions were computed using OLS (Ordinary Least Squares). Significance threshold: **α = 0.05**.

---

## 4. Dependent and Independent Variables

| Role | Variable(s) |
|---|---|
| **Dependent (Y)** | `monthly_sales` |
| **Numerical X** | marketing_spend, footfall, avg_discount_pct, staff_count, inventory_availability_pct, competitor_distance_km, holiday_flag, customer_rating |
| **Categorical X (dummy-encoded)** | region (East = reference), store_type (Airport = reference) |
| **Excluded** | store_id, month, monthly_profit |

---

## 5. Dummy Variable Approach

Two categorical variables were encoded using **k−1 dummy coding** to avoid the dummy variable trap (perfect multicollinearity):

| Variable | k levels | Reference category | Dummies created |
|---|---|---|---|
| region | 4 | **East** (largest sample, 104 rows) | region_North, region_South, region_West |
| store_type | 4 | **Airport** (highest avg sales, $716K) | store_type_High Street, store_type_Mall, store_type_Residential |

Coefficients on the dummies are interpreted *relative to the reference*. For example, `region_West = +25,291` means West-region stores sell ~$25K more per month than East-region stores, holding all numeric predictors constant.

Full encoding explanation: `outputs/model_equations.md`.

---

## 6. Model Comparison Summary

| Model | Type | Predictors | R² | Adj R² | Selected? |
|---|---|---|---|---|---|
| M1 | Simple | footfall | 0.7363 | 0.7355 | No |
| M2 | Simple | marketing_spend | 0.1672 | 0.1646 | No |
| M3 | Simple | staff_count | 0.6523 | 0.6512 | No |
| **M4** | **Multiple** | **8 numeric + 6 dummies** | **0.8570** | **0.8504** | **★ YES** |

M4 explains 85.7% of monthly-sales variance — substantially more than any single-predictor model. Adjusted R² also remains high (0.850), so the added variables earn their place.

Detailed comparison in `analysis/model_comparison.md`.

---

## 7. Final Model Selected: M4 (Multiple Regression)

> **monthly_sales = 86,319.14**
> **+ 1.21 × marketing_spend**
> **+ 27.34 × footfall**
> **− 41,243 × avg_discount_pct**
> **+ 3,508 × staff_count**
> **+ 3,062 × inventory_availability_pct**
> **− 3,438 × competitor_distance_km**
> **+ 15,157 × holiday_flag**
> **+ 12,509 × customer_rating**
> **+ 9,949 × region_North + 21,090 × region_South + 25,291 × region_West**
> **− 24,476 × store_type_High Street − 11,862 × store_type_Mall − 44,695 × store_type_Residential**

**Fit:** R² = 0.857, Adjusted R² = 0.850, F = 130.55, p < 0.001, n = 320.

### Why M4 was selected

1. Highest R² and adjusted R²
2. Most coefficients statistically significant
3. Controls for confounding (e.g., staff_count effect drops from ~$17K → $3.5K after controlling for footfall — the truer estimate)
4. Surfaces effects that simple regression hides (e.g., customer_rating significance emerges only here)
5. Captures categorical effects via dummy coefficients — directly actionable for region and store-type decisions

---

## 8. Business Recommendation

**Headline:** prioritise initiatives that drive footfall and maintain high inventory availability. Sustain marketing spend but cap the high-end where diminishing returns are evident. Pause aggressive discounting — no statistical evidence it drives sales. For new-store decisions, prioritise Airport and Mall locations over Residential.

### Top actions (in order of evidence strength)

1. **Set a 90% minimum inventory-availability target.** Each percentage point of availability adds ~$3K monthly (p < 0.001).
2. **Drive footfall through location, signage, partnerships.** Each visitor adds ~$27 in sales.
3. **Cap high marketing spends** (>$130K/month). Residual analysis shows diminishing returns; reallocate to under-spent stores where marginal returns are stronger.
4. **Audit top over-performers** (STR-1026, STR-1028, STR-1051, STR-1058, STR-1073) — all East-region stores that beat the model by $85K–$111K. Find what they're doing right.
5. **Pause discount escalation** — `avg_discount_pct` shows no significant sales lift.
6. **Investigate Residential store under-performance** — they sell $45K less than Airport stores even after controlling for footfall and marketing.

Full reasoning, evidence trail, and prioritised action list: `outputs/final_recommendation.md`.

---

## 9. Assumptions and Limitations

### Assumptions made
- Missing values are missing-at-random and adequately handled by median imputation
- Relationships are approximately linear in the observed range
- No interaction effects between predictors (e.g., marketing doesn't behave differently for Mall vs Airport stores)
- The 4-month window (Jan–Apr 2025) is representative for the recommendations
- OLS assumptions (independence, homoscedasticity, normality of residuals) approximately hold

### Limitations
1. **Association is not causation.** Regression shows variables move together; it does not prove one causes the other. A/B experiments are required for true causal claims.
2. **Direction of causality may be reversed.** Staff_count → sales might run the other way: successful stores hire more staff.
3. **Omitted variables.** We don't observe store-manager quality, basket size, repeat-customer rate, local economic conditions — any of which could be the true drivers behind the variables we measure.
4. **Counterintuitive coefficients should be interpreted carefully.** competitor_distance_km has a negative sign — likely a proxy for low-density locations, not a causal effect.
5. **Linear functional form.** Real-world relationships often diminish at extremes (e.g., marketing has diminishing returns). The residual analysis confirms this for marketing.
6. **Small sample for subgroups.** 320 observations and 14 predictors is fine for the main model but doesn't support fine-grained interaction analyses.
7. **No external validation.** The model was not tested on a held-out dataset; reported R² is in-sample.

---

## 10. Repository Contents & Screenshots

```
part3_regression_insights/
├── data/
│   └── business_regression_data.xlsx     Raw dataset
├── analysis/
│   ├── regression_workbook.xlsx          7-sheet workbook: original, cleaned, dummies, simple regs, multiple reg, residuals, comparison
│   ├── model_comparison.md               Detailed side-by-side comparison of M1–M4
│   └── residual_analysis.md              Top residuals, patterns, business implications
├── outputs/
│   ├── regression_summary.xlsx           Clean comparison table + final-model coefficients
│   ├── final_recommendation.md           Decision memo for leadership
│   └── model_equations.md                All equations + coefficient interpretations + final-model selection rationale
├── screenshots/
│   ├── simple_regression_output.png      M1 (Sales ~ footfall) output + scatter
│   ├── multiple_regression_output.png    M4 full output with key findings
│   ├── residuals_preview.png             Predicted vs actual + residuals tables
│   └── model_comparison_preview.png      4-model comparison + R² bar chart
└── README.md                              This file
```

### Screenshots included

| File | Content |
|---|---|
| `simple_regression_output.png` | M1 regression output (coefficient table + scatter with fitted line) |
| `multiple_regression_output.png` | M4 (final model) coefficient table, fit statistics, and key findings |
| `residuals_preview.png` | Actual vs predicted scatter, residuals vs predicted scatter, plus top-5 positive and negative residual tables |
| `model_comparison_preview.png` | Side-by-side comparison of M1–M4 with R² bar chart and selection rationale |
