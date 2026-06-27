# Residual Analysis

This document examines the prediction errors (residuals) of the final multiple regression model (M4) — what the model gets wrong, where it gets things wrong, and what business stories the errors might be telling us.

A residual is calculated as:

> **residual = actual_sales − predicted_sales**

A positive residual means the store sold **more** than the model expected (the model under-predicts).
A negative residual means the store sold **less** than the model expected (the model over-predicts).

---

## 1. Overall Residual Statistics

| Statistic | Value |
|---|---|
| Mean residual | $0 (forced to zero by OLS — expected) |
| Standard deviation | $39,243 |
| Min (most over-predicted) | −$136,867 |
| Max (most under-predicted) | +$111,017 |
| Residual SE as % of avg sales | ~5.8% |

A standard deviation of ~$39K on average sales of ~$681K means typical errors are about 6% of sales — reasonable for a model with R² = 0.857, and acceptable for the kind of strategic decisions leadership is contemplating.

---

## 2. Top 5 Positive Residuals — Model UNDER-predicts

These stores beat the model's prediction by the largest margins. They are doing something the model can't explain from the available features.

| # | store_id | Month | Region | Store Type | Footfall | Marketing | Actual | Predicted | Residual |
|---|---|---|---|---|---|---|---|---|---|
| 1 | STR-1058 | 2025-02 | East | High Street | 8,961 | $113.7K | $870.9K | $759.9K | **+$111,017** |
| 2 | STR-1028 | 2025-04 | East | Mall | 4,063 | $52.8K | $713.6K | $610.5K | **+$103,079** |
| 3 | STR-1073 | 2025-03 | East | Residential | 8,300 | $56.7K | $813.3K | $721.4K | **+$91,901** |
| 4 | STR-1051 | 2025-02 | East | Airport | 6,389 | $78.1K | $787.7K | $701.4K | **+$86,334** |
| 5 | STR-1026 | 2025-04 | East | Mall | 2,414 | $32.3K | $625.5K | $540.2K | **+$85,279** |

### Business interpretation

- **All 5 are East-region stores.** This is the most striking pattern. East is the reference category in the dummy encoding (regional coefficient = 0), so the model effectively treats East as "average." These stores show that some East-region stores are doing materially better than the East baseline suggests.
- **High revenue per visitor.** STR-1026 stands out — only 2,414 visitors but $626K in sales (about $259/visitor). The Mall average is far lower. Something about this store (basket size, product mix, premium positioning, loyalty programme?) lets it monetise traffic unusually well.
- **STR-1028 and STR-1073 also show high revenue per visitor.** Both are East-region stores under 8,300 footfall but with sales > $700K.
- **Possible drivers the model can't see:** customer segment, average basket size, repeat-customer rate, product mix, in-store experience quality. Worth investigating these stores qualitatively.

### Recommended action
**Visit/audit STR-1026 and STR-1028 in particular.** Find out what they're doing right. If it's replicable (in-store experience, merchandising, staff training), it might be the highest-ROI operational lever in the dataset — far more than another dollar of marketing.

---

## 3. Top 5 Negative Residuals — Model OVER-predicts

These stores fell short of what the model expected, given their observable inputs.

| # | store_id | Month | Region | Store Type | Footfall | Marketing | Actual | Predicted | Residual |
|---|---|---|---|---|---|---|---|---|---|
| 1 | STR-1023 | 2025-02 | South | Mall | 5,400 | $141.5K | $627.2K | $764.0K | **−$136,867** |
| 2 | STR-1017 | 2025-03 | West | High Street | 9,628 | $79.3K | $685.4K | $805.3K | **−$119,901** |
| 3 | STR-1012 | 2025-01 | West | Residential | 5,145 | $138.9K | $595.5K | $709.9K | **−$114,446** |
| 4 | STR-1007 | 2025-02 | West | Mall | 8,927 | $172.4K | $800.5K | $912.3K | **−$111,893** |
| 5 | STR-1060 | 2025-01 | West | Mall | 9,908 | $46.5K | $721.1K | $808.0K | **−$86,942** |

### Business interpretation

- **4 of 5 are West-region stores.** West has the largest positive regional dummy (+$25,291), meaning the model assumes West stores systematically sell more. These stores show that the West-region uplift may not apply uniformly — some West stores significantly under-perform expectations.
- **3 of 5 have very high marketing spend** ($138K–$172K), well above the dataset median of $55K. Yet sales did not rise proportionally. This is a strong signal of **diminishing returns on marketing**.
  - STR-1007 spent $172K (highest in the dataset) but sold $800K — the model expected over $900K
  - STR-1023 spent $141K but sold only $627K
  - STR-1012 spent $139K but sold only $595K
- **The model assumes a linear $1.21 return per $1 of marketing.** In reality, returns likely diminish at high spend levels. The linear model is not capturing this curvature.

### Recommended action
**Re-evaluate the highest marketing spends.** STR-1007, STR-1012, and STR-1023 are spending substantially more than peers without proportional sales benefit. A marketing-spend cap, or reallocation toward stores with lower current spend (where marginal returns may still be strong), could improve total ROI.

---

## 4. Patterns and What They Mean

### Regional asymmetry

| Region | Residual mean | Residual std | Observations |
|---|---|---|---|
| East | $0 | $41,141 | 104 |
| North | $0 | $34,997 | 64 |
| South | $0 | $39,426 | 60 |
| West | $0 | $40,337 | 92 |

Mean residuals are zero by region because regional dummies are in the model — this is mechanical, not informative. But two interesting patterns emerge from the *extremes*:

- **Positive extremes cluster in East.** The model's "East = reference" assumption may be hiding a sub-segment of East stores that systematically over-perform.
- **Negative extremes cluster in West.** The West dummy ($+25,291) may be over-applied — there's a sub-population of West stores that don't get the full West uplift, particularly when paired with very high marketing spend.

### Store-type observations

Residual standard deviation by store type:
- Mall: $42,867 (highest variability — the model is least precise for Mall stores)
- Airport: $41,453
- Residential: $38,303
- High Street: $37,511 (most consistent)

Mall stores are the hardest to predict — they have a wide range of behaviours that the linear model doesn't fully capture.

### Time pattern

| Month | Mean residual | Observations |
|---|---|---|
| Jan 2025 | −$6,698 | 80 |
| Feb 2025 | +$2,966 | 80 |
| Mar 2025 | +$65 | 80 |
| Apr 2025 | +$3,666 | 80 |

January is slightly over-predicted (residuals average −$6.7K). This is mild and within noise, but consistent with post-holiday softness that holiday_flag alone may not fully capture.

---

## 5. Is the Model Systematically Biased?

**No — but it has known blind spots.**

The model is **mean-unbiased by construction** for every category included in the dummies (region, store_type) and the dataset as a whole. The mean residual is exactly $0 within each region and store_type.

However:
1. **Linear marketing assumption breaks at the high end.** Stores spending > $130K/month are being over-predicted. This is a curvature issue that linear regression cannot capture without a transformation (e.g., log(marketing_spend) or a quadratic term).
2. **East-region outliers are systematic, not random.** The fact that all 5 top positive residuals are East stores suggests there's an unobserved East-region success factor (e.g., a specific store-manager initiative, a regional product launch) that the model can't see.
3. **Variability is higher for Mall stores.** The Mall coefficient (−$11,862, p = 0.187) is not even statistically significant, suggesting Mall is a heterogeneous category. Sub-categorising malls (size, anchor tenants) might help.

---

## 6. Action Items from the Residual Analysis

1. **Investigate the top 5 over-performers** (STR-1058, STR-1028, STR-1073, STR-1051, STR-1026). Document what they're doing differently. If replicable, this is the highest-ROI initiative in the data.
2. **Review marketing budgets > $130K/month.** Likely diminishing returns; reallocate to under-spent stores where marginal sales response is higher.
3. **Re-examine the West-region uplift assumption.** It applies on average, but several individual West stores under-perform. A more granular sub-regional or store-cluster analysis could refine this.
4. **Consider a non-linear treatment for marketing_spend** in future iterations — either log-transform the variable or add a quadratic term. This is beyond the scope of this analysis but is a natural follow-up.
5. **Acknowledge that the model is descriptive, not prescriptive.** Even an 85.7%-R² model leaves 14% unexplained, and the residuals point to specific stores and conditions where the model misses.

For full coefficient details, see `outputs/model_equations.md`.
