# Model Equations

This document records every regression equation produced in the analysis, the meaning of each coefficient in business terms, the dummy-variable encoding scheme, and the final model selected.

---

## 1. Dummy Variable Approach

Two categorical variables were encoded:

### Region (4 categories → 3 dummies)

| Region | region_North | region_South | region_West |
|---|---|---|---|
| **East** *(reference)* | 0 | 0 | 0 |
| North | 1 | 0 | 0 |
| South | 0 | 1 | 0 |
| West | 0 | 0 | 1 |

**East was chosen as the reference category** because it has the largest representation in the dataset (104 of 320 rows). All region coefficients in the multiple regression are interpreted *relative to East*.

### Store Type (4 categories → 3 dummies)

| Store Type | store_type_High Street | store_type_Mall | store_type_Residential |
|---|---|---|---|
| **Airport** *(reference)* | 0 | 0 | 0 |
| High Street | 1 | 0 | 0 |
| Mall | 0 | 1 | 0 |
| Residential | 0 | 0 | 1 |

**Airport was chosen as the reference** because it has the highest average monthly sales (~$716K) among store types. This is a deliberate choice — making the highest baseline the reference means the other three coefficients will be **negative** (or near zero), which is the most intuitive direction to read: "how much less do other store types sell compared to the best."

### Why k−1 dummies?

A categorical variable with k levels is encoded with k−1 dummies, not k. Using all k would create perfect multicollinearity (the dummies sum to 1 for every row, redundantly conveying the intercept). The omitted level becomes the reference, and the intercept now represents the expected value when *all dummies = 0* (i.e. when the row belongs to the reference category).

---

## 2. Simple Regression Equations

Each simple model uses one predictor only:

### Model M1: Sales ~ footfall

> **monthly_sales = 446,373.18 + 35.6800 × footfall**

- **Intercept (446,373):** the expected monthly sales of a store with zero footfall — not literally meaningful (no real store has zero footfall), but anchors the line
- **Coefficient on footfall (35.68):** each additional store visit is associated with about **$35.68** more in monthly sales
- **R² = 0.7363** — footfall alone explains 73.6% of monthly-sales variation
- **p < 0.001** — relationship is highly statistically significant

### Model M2: Sales ~ marketing_spend

> **monthly_sales = 560,690.04 + 2.1305 × marketing_spend**

- **Intercept (560,690):** baseline sales when marketing_spend = 0
- **Coefficient (2.13):** each additional **$1** in marketing spend is associated with about **$2.13** more in sales
- **R² = 0.1672** — only ~17% of variance explained; many other factors matter more
- **p < 0.001** — coefficient is statistically reliable, but the model alone is weak

### Model M3: Sales ~ staff_count

> **monthly_sales = 400,564.81 + 16,984.03 × staff_count**

- **Intercept (400,565):** baseline sales for a store with zero staff (theoretical)
- **Coefficient (16,984):** each additional staff member is associated with about **$17K** more in monthly sales
- **R² = 0.6523** — strong, but likely confounded: busier stores hire more staff, so this captures volume more than productivity
- **p < 0.001** — relationship is highly significant

---

## 3. Multiple Regression Equation (Final Model)

> **monthly_sales = 86,319.14**
> **+ 1.2057 × marketing_spend**
> **+ 27.3380 × footfall**
> **− 41,243.15 × avg_discount_pct**
> **+ 3,508.40 × staff_count**
> **+ 3,062.21 × inventory_availability_pct**
> **− 3,438.11 × competitor_distance_km**
> **+ 15,157.29 × holiday_flag**
> **+ 12,509.46 × customer_rating**
> **+ 9,948.71 × region_North**
> **+ 21,089.57 × region_South**
> **+ 25,291.42 × region_West**
> **− 24,475.84 × store_type_High Street**
> **− 11,862.24 × store_type_Mall**
> **− 44,695.09 × store_type_Residential**

**Model fit:** R² = 0.8570, Adjusted R² = 0.8504, F = 130.55, p < 0.001, n = 320

### Coefficient interpretations in plain English

| Variable | Coefficient | Plain-English interpretation | p-value |
|---|---|---|---|
| Intercept | $86,319 | Expected sales for an East-region Airport store, holding all numeric predictors at zero (theoretical baseline) | 0.056 |
| marketing_spend | +$1.21 | Each $1 of marketing spend is associated with +$1.21 in sales — positive ROI | <0.001 |
| footfall | +$27.34 | Each additional visitor is associated with +$27.34 in sales — the strongest economic effect | <0.001 |
| avg_discount_pct | −$41,243 | Each +1 percentage-point of discount is associated with −$412 in sales, but **not statistically significant** — discounting strategy does not drive sales here | 0.224 |
| staff_count | +$3,508 | Each additional staff member adds ~$3.5K in sales, holding footfall constant | 0.003 |
| inventory_availability_pct | +$3,062 | Each +1pp of inventory availability adds ~$3K in sales — strong operational lever | <0.001 |
| competitor_distance_km | −$3,438 | **Counterintuitive:** stores farther from competitors sell *less*. Likely a proxy for low-density / suburban locations | <0.001 |
| holiday_flag | +$15,157 | Months with holidays show ~$15K higher sales | 0.014 |
| customer_rating | +$12,509 | Each +1 point in customer rating adds ~$12.5K in sales — emerges only after controlling for other factors | 0.005 |
| region_North | +$9,949 | North region stores sell ~$10K more than East, **not significant** | 0.131 |
| region_South | +$21,090 | South region stores sell ~$21K more than East | 0.002 |
| region_West | +$25,291 | West region stores sell ~$25K more than East — strongest regional effect | <0.001 |
| store_type_High Street | −$24,476 | High Street stores sell ~$24K less than Airport (reference) | 0.005 |
| store_type_Mall | −$11,862 | Mall stores sell ~$12K less than Airport, **not significant** | 0.187 |
| store_type_Residential | −$44,695 | Residential stores sell ~$45K less than Airport — largest negative store-type effect | <0.001 |

### How dummy coefficients work

A region or store-type coefficient answers: *"holding everything else constant, how does this category compare to the reference?"* So `+25,291` for region_West means a West store, with all other characteristics identical to an East store, sells about $25K more per month.

The four numeric variables that are most important (footfall, marketing_spend, inventory_availability_pct, competitor_distance_km) all have p < 0.001 — extremely strong statistical evidence.

---

## 4. Final Model Selected

**M4 (Multiple Regression) is selected as the final model.**

### Why M4 over M1, M2, or M3?

| Criterion | M1 (footfall) | M2 (marketing) | M3 (staff) | M4 (Multiple) |
|---|---|---|---|---|
| R² | 0.736 | 0.167 | 0.652 | **0.857** |
| Adjusted R² | 0.735 | 0.165 | 0.651 | **0.850** |
| Captures region effects | No | No | No | **Yes** |
| Captures store-type effects | No | No | No | **Yes** |
| Confounding controlled | No | No | No | **Yes** |
| Actionable for leadership | Limited | Limited | Limited | **Yes** |

1. **M4 explains 85.7% of variance** — far more than any simple model.
2. **Adjusted R² remains high at 0.850** — the added variables earn their place; this is not just over-fitting.
3. **M4 controls for confounding.** In M3 (staff_count alone), the coefficient on staff was ~$17,000 per staff member; in M4 it drops to $3,508 because footfall absorbs most of that effect. M3 was capturing "busy stores have more staff," not the marginal value of staff itself. M4 tells the truer story.
4. **M4 surfaces effects that simple models miss.** customer_rating had no apparent effect in simple regression (r ≈ −0.03, p = 0.64), but emerges as significant (+$12,509/point, p = 0.005) once footfall and store type are controlled. This is a *suppressor effect* and a key reason multiple regression is more informative.
5. **M4 produces actionable categorical insights.** Knowing that Residential stores under-perform by ~$45K vs Airport, even after controlling for footfall and marketing, lets leadership ask the right follow-up questions.

### Why not include even more variables?

Adding more predictors always raises R². The check is whether **adjusted R²** also rises. In this dataset, the 14 predictors used in M4 give a strong adjusted R² (0.850). Adding interaction terms or transformations (e.g. log(footfall)) was not explored further; M4 already explains most of the variance, and additional complexity would be hard to communicate to leadership.

---

## 5. Diagnostic Notes

- **No multicollinearity disasters:** the largest pairwise correlation among predictors is footfall vs staff_count (~0.70); both retain significance in M4, so VIFs are tolerable.
- **Residuals approximately mean-zero** with standard deviation ~$39K (about 6% of average sales). See `residual_analysis.md`.
- **No transformation of the dependent variable** was applied. Sales is approximately normal and the residual distribution is symmetric.
