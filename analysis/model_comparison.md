# Model Comparison

This document compares the four regression models built in this analysis: three simple regressions (M1–M3) and one multiple regression (M4). The goal is to demonstrate why a multiple regression model is required to answer the business question and to identify the model best suited for leadership decision-making.

---

## 1. Summary Comparison Table

| | **M1** | **M2** | **M3** | **M4** |
|---|---|---|---|---|
| **Type** | Simple | Simple | Simple | Multiple |
| **Dependent variable** | monthly_sales | monthly_sales | monthly_sales | monthly_sales |
| **Predictors** | footfall | marketing_spend | staff_count | 8 numeric + 6 dummies (14 total) |
| **R²** | 0.7363 | 0.1672 | 0.6523 | **0.8570** |
| **Adjusted R²** | 0.7355 | 0.1646 | 0.6512 | **0.8504** |
| **F-statistic** | 887.96 | 63.84 | 596.62 | **130.55** |
| **F p-value** | < 0.001 | < 0.001 | < 0.001 | **< 0.001** |
| **Observations** | 320 | 320 | 320 | 320 |
| **Decision** | Strong baseline, not enough | Weak | Strong but confounded | **★ SELECTED** |

---

## 2. Significant Variables in Each Model

### M1: Sales ~ footfall
| Variable | Coefficient | p-value | Significant? |
|---|---|---|---|
| Intercept | 446,373 | < 0.001 | ✓ |
| footfall | 35.68 | < 0.001 | ✓ |

### M2: Sales ~ marketing_spend
| Variable | Coefficient | p-value | Significant? |
|---|---|---|---|
| Intercept | 560,690 | < 0.001 | ✓ |
| marketing_spend | 2.13 | < 0.001 | ✓ |

### M3: Sales ~ staff_count
| Variable | Coefficient | p-value | Significant? |
|---|---|---|---|
| Intercept | 400,565 | < 0.001 | ✓ |
| staff_count | 16,984 | < 0.001 | ✓ |

### M4: Multiple Regression (significant variables only, p < 0.05)
| Variable | Coefficient | p-value |
|---|---|---|
| marketing_spend | +1.21 | < 0.001 |
| footfall | +27.34 | < 0.001 |
| staff_count | +3,508 | 0.003 |
| inventory_availability_pct | +3,062 | < 0.001 |
| competitor_distance_km | −3,438 | < 0.001 |
| holiday_flag | +15,157 | 0.014 |
| customer_rating | +12,509 | 0.005 |
| region_South | +21,090 | 0.002 |
| region_West | +25,291 | < 0.001 |
| store_type_High Street | −24,476 | 0.005 |
| store_type_Residential | −44,695 | < 0.001 |

**Not statistically significant in M4:** avg_discount_pct (p = 0.224), region_North (p = 0.131), store_type_Mall (p = 0.187), intercept (p = 0.056).

---

## 3. Business Usefulness

### M1 (footfall only) — *useful as a sanity check, not as a decision tool*
- **Pro:** clear, easy to explain, R² of 0.74 is impressive for one variable
- **Con:** offers leadership only one lever — "drive more traffic" — which is a generic instruction. It does not tell us whether marketing, inventory, staffing, or location investments matter
- **Pro:** the coefficient (~$35/visitor) gives a useful unit-economics number for cost-of-traffic decisions

### M2 (marketing_spend only) — *misleading on its own*
- **Pro:** the +$2.13 sales per $1 marketing coefficient looks attractive
- **Con:** R² is only 17% — marketing is far from the dominant driver. Leadership could easily over-interpret this as "increase marketing budget" without realising other factors swamp it
- **Con:** does not control for store type or region; the coefficient may be biased

### M3 (staff_count only) — *correlation, not driver*
- **Pro:** strong R² (0.65)
- **Con:** **almost certainly confounded.** Busy stores hire more staff because they have more sales, not the other way around. The huge coefficient ($17K per staff member) drops to $3,508 in M4 once footfall is controlled for, which is the more defensible estimate of staff productivity
- **Lesson:** simple regression cannot separate "more staff causes more sales" from "more sales causes more staff." Multiple regression makes this less ambiguous (though never definitive without an experiment)

### M4 (Multiple) — *the decision tool*
- **Pro:** captures the most variance (85.7%)
- **Pro:** disentangles individual contributions of every observable factor
- **Pro:** surfaces effects that simple regression hides (e.g., customer_rating becomes significant only after controlling for store type)
- **Pro:** controls for confounding (staff_count effect drops 5×; tells the truer story)
- **Pro:** answers segmented questions (regions and store types) directly via dummy coefficients
- **Con:** still descriptive, not causal — no experiment was run
- **Con:** the negative competitor_distance_km coefficient is counterintuitive and needs careful explanation to non-statistical stakeholders

---

## 4. Limitations Common to All Four Models

1. **None of these models can establish causation.** They show statistical *association* between predictors and sales. To know whether spending $1 more on marketing *causes* $1.21 more in sales, an experiment (e.g., A/B test of marketing budget) is required.
2. **No interaction terms.** We assume each predictor has the same effect regardless of the values of the others (e.g., marketing might be more effective for Airport stores than Residential — we don't test this).
3. **Linear functional form.** We assume effects are additive and linear. In reality, marketing spend likely has diminishing returns; the linear coefficient is an average across the observed range.
4. **Only 320 observations** (80 stores × 4 months). This is decent for a 14-predictor model (the rule of thumb is ~20 observations per predictor; we have 23) but doesn't support fine subgroup analysis.
5. **Cross-sectional snapshot.** All data is from Jan–Apr 2025. Effects could differ in other seasons (the holiday_flag captures some of this).
6. **Selection effects.** We have no data on stores that were considered and rejected; the 80 stores in the sample may not represent all possible stores.

### Additional limitations of M4 specifically

- **competitor_distance_km coefficient is counterintuitive and may be capturing an omitted variable** (urban density, foot-traffic ecosystem). Interpret cautiously.
- **customer_rating coefficient flips between simple and multiple** — a sign that the simple effect is masked by other variables; the M4 estimate is more reliable but harder to communicate.
- **Adjusted R² is excellent (0.850)** but residuals still have ~$39K standard deviation — meaningful for a sales prediction at this scale (~6% of average sales).

---

## 5. Verdict

**M4 (Multiple Regression) is the recommended model for leadership decision-making.** It explains 85.7% of monthly-sales variance, isolates the contribution of each operational lever, and provides actionable estimates for both numerical drivers and categorical effects (region, store type). Simple regressions M1–M3 are retained as reference points but are insufficient on their own for the kinds of decisions leadership is contemplating (marketing-spend allocation, staffing, inventory targets, store-type prioritisation).

See `outputs/regression_summary.xlsx` for a side-by-side numerical comparison and `outputs/model_equations.md` for full coefficient interpretations.
