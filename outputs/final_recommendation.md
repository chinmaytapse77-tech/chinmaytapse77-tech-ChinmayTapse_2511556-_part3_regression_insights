# Final Recommendation — Drivers of Monthly Sales

**To:** Leadership Team
**From:** Business Analyst
**Re:** What factors most strongly drive monthly store sales, and where should we focus operationally?

---

## Executive Summary

A multiple regression model fit on 320 store-months (80 stores × 4 months, Jan–Apr 2025) explains **85.7% of variance** in monthly sales. The most strongly associated drivers of sales — in descending order of business importance — are **store footfall, marketing spend, inventory availability, and store type (Airport > Mall ≈ High Street > Residential)**. Discount strategy shows **no significant association** with sales. Customer rating matters once other factors are controlled, but the effect is moderate.

> **Headline recommendation:** prioritise initiatives that drive footfall and maintain high inventory availability. Increase marketing spend selectively (current evidence supports $1.21 sales per $1 of marketing, but with diminishing returns at very high spend). Re-examine the discount strategy — it does not appear to be generating sales lift. For store-type prioritisation, **Airport and Mall stores deliver materially more sales than Residential stores** after controlling for footfall and marketing.

---

## 1. Which Factors Are Most Strongly Associated with Sales?

Ranked by strength of statistical evidence and business meaningfulness:

| Rank | Factor | Coefficient (M4) | p-value | Business meaning |
|---|---|---|---|---|
| 1 | **footfall** | +$27.34 per visitor | < 0.001 | Each additional store visit adds ~$27 in monthly sales. Dominant driver. |
| 2 | **store_type = Residential** (vs Airport) | −$44,695 | < 0.001 | Residential stores systematically under-perform Airport stores by ~$45K/month |
| 3 | **competitor_distance_km** | −$3,438 per km | < 0.001 | Each km farther from competitors = ~$3.4K less in sales. Counterintuitive — likely a proxy for low-density location |
| 4 | **region = West** (vs East) | +$25,291 | < 0.001 | West-region stores out-sell East by ~$25K |
| 5 | **inventory_availability_pct** | +$3,062 per pp | < 0.001 | Each +1 pp of in-stock availability adds ~$3K in sales |
| 6 | **marketing_spend** | +$1.21 per $1 | < 0.001 | Marketing has positive ROI on average — $1 in adds $1.21 in sales |
| 7 | **store_type = High Street** (vs Airport) | −$24,476 | 0.005 | High Street stores sell ~$24K less than Airport |
| 8 | **customer_rating** | +$12,509 per point | 0.005 | Each +1 rating point adds ~$12.5K, **after** controlling for other factors |
| 9 | **staff_count** | +$3,508 per staff | 0.003 | Each additional staff member adds ~$3.5K. (Note: much smaller than simple regression suggested — simple model was confounded) |
| 10 | **region = South** (vs East) | +$21,090 | 0.002 | South stores out-sell East by ~$21K |
| 11 | **holiday_flag** | +$15,157 in holiday months | 0.014 | Holiday months show ~$15K higher sales |

**Variables that did NOT show significant association:**
- `avg_discount_pct` (p = 0.224) — deeper discounting does not appear to drive sales
- `region_North` (p = 0.131) — North-region effect indistinguishable from East
- `store_type_Mall` (p = 0.187) — Mall vs Airport difference not significant

---

## 2. Where Leadership Should Focus

### Tier 1 — Strongest evidence, clearest business levers

#### **Drive footfall**
Footfall is the dominant predictor. Initiatives that bring more people into stores — location decisions, signage, partnerships, local events — give the most direct return on the model's evidence. At ~$27 per visitor, even modest footfall improvements (e.g., +500 visitors/month for a typical store) translate to +$13.5K monthly sales.

#### **Defend inventory availability**
Each +1 percentage point of in-stock availability adds ~$3K in monthly sales (p < 0.001). Stores currently below 85% availability are losing real revenue. Tightening replenishment cycles is a low-cost, high-confidence improvement.

#### **Sustain marketing spend, but cap the extremes**
The average return is positive ($1.21 per $1). But the residual analysis (`analysis/residual_analysis.md`) shows that stores spending >$130K/month consistently under-perform their model-predicted sales — strong evidence of **diminishing returns**. Reallocate from over-spending stores to under-spending stores, where marginal returns are likely higher.

### Tier 2 — Significant but harder to act on directly

#### **Store-type mix**
Airport stores out-sell Residential stores by ~$45K/month even after controlling for footfall and marketing. If new-store decisions are pending, this is meaningful. But re-classifying existing Residential stores is not feasible — interpret this as a **future-investment guidance**, not an operational lever.

#### **Customer rating**
A +1 point in rating is associated with +$12.5K in sales — but this effect only emerged in the multiple regression (not in simple regression), suggesting it's confounded with store type. Operational investment in CX (training, complaint resolution) is reasonable but the link to sales is more correlational than the others.

### Tier 3 — Do NOT over-interpret

#### **Discount strategy**
`avg_discount_pct` is **not statistically significant** (p = 0.224). The model finds no evidence that deeper discounting drives more sales in this dataset. Leadership should not assume "discount more to sell more" — and may want to **investigate whether current discounts are simply giving away margin without generating volume**.

#### **`competitor_distance_km` (negative coefficient)**
The sign is counterintuitive (more distance = less sales). This is almost certainly the variable acting as a **proxy for location density** — stores far from competitors tend to be in low-traffic suburban or rural areas. **Do not interpret this as "open stores near competitors to sell more."** It's a feature of the available data, not a causal recipe.

#### **`staff_count`**
The simple regression suggested each staff member added $17K in sales — almost certainly an artifact of busy stores hiring more staff. The multiple regression value ($3,508) is more honest but still descriptive. **Do not use this to justify staffing decisions without an experiment.**

---

## 3. Recommended Business Actions

1. **Conduct a "what-do-they-do-right" audit on top over-performers** (STR-1026, STR-1028, STR-1051, STR-1058, STR-1073). All five East-region stores beat the model by $85K–$111K. If their practices are replicable, this is likely the highest-ROI single initiative in the data.
2. **Cap or rebalance high marketing spends.** Stores with >$130K monthly marketing spend show diminishing returns. Reallocate ~$50K/store from these to lower-spend stores where marginal sales response is stronger.
3. **Set a minimum inventory-availability target of 90%.** Below this, the model says each missing percentage point costs ~$3K/month. Cost of reaching 90% is operational; benefit is direct.
4. **Pause discount escalation.** No evidence in this dataset that deeper discounts drive incremental sales. Either narrow discount scope or run a controlled experiment to test causation.
5. **Document and rate Residential store performance.** Residential stores under-perform Airport by ~$45K/month even after controls. Before any further Residential expansion, identify whether this is a fixable operational issue or a structural limitation.
6. **For new-store decisions**, prioritize Airport and Mall locations over High Street and Residential, all else equal.

---

## 4. Risks and Limitations

### What this analysis CAN tell leadership
- Which factors are statistically associated with sales in the observed 4-month window
- The approximate magnitude of those associations
- Which stores and conditions the model fits well or poorly
- Which variables are likely red herrings (e.g., discount, staff_count alone)

### What it CANNOT tell leadership
1. **Causation.** Regression shows *association*, not *cause-and-effect*. We cannot claim "$1 of marketing causes $1.21 of sales." To know that, we would need a randomized A/B test: vary marketing spend within comparable stores and observe sales response. Until such an experiment is run, the recommendation to "sustain marketing" is reasonable but not proven.
2. **Direction of causality.** The strong staff_count effect in the simple model is the classic example — busy stores hire more staff, not the other way around. Always ask "could this run in reverse?" before acting on a regression coefficient.
3. **Generalisability outside Jan–Apr 2025.** Effects could differ in other seasons. Holiday months show $15K higher sales, but this is captured by one binary flag — finer-grained seasonality is not.
4. **Diminishing returns.** The linear model assumes a constant per-unit effect. In reality, very high marketing spend, very high staffing, etc. likely have curvature. The residual analysis confirms this for marketing.
5. **Unobserved confounders.** The model uses only the available 14 predictors. Things we *don't* measure — store-manager quality, local economic shocks, product mix, basket size, repeat customer rate — could be the true drivers of much of what we see.
6. **Counterintuitive coefficients are a warning sign.** competitor_distance_km being negative is almost certainly a proxy effect, not a causal one. Without spatial or density data, we can't disentangle this.
7. **Sample size limits subgroup analysis.** With 320 observations and 14 predictors, we have adequate power for the main model but not for fine-grained interactions (e.g., does marketing work better for Mall vs Airport stores?).

---

## 5. Why Regression Shows Association, Not Causation

This is worth stating clearly because it's the single biggest risk in acting on regression results:

> **Regression measures how variables *move together*, not how one *causes* another.**

A coefficient of +$1.21 for marketing_spend means "in our 320 store-months, stores that spent $1 more on marketing also tended to have $1.21 more in sales, after accounting for other factors." It does **not** mean "if we increase marketing by $1, we will get $1.21 more in sales."

Reasons regression-association can fail to be causation:
1. **Reverse causation.** Stores that already sell well might choose to invest more in marketing (because they have the budget) — so high sales causes high marketing spend, not the other way around.
2. **Omitted variables.** A third unmeasured factor (e.g., store-manager skill, local competition intensity, regional economic strength) could drive both higher marketing spend and higher sales simultaneously.
3. **Selection.** The stores in our dataset may not be representative of all possible stores. The estimated effects apply to the kinds of stores we observed, not necessarily to new ones.
4. **Functional-form assumptions.** Linear regression assumes a constant per-unit effect. If the true relationship is non-linear (e.g., diminishing returns on marketing), the linear coefficient is an average across the observed range — and may mislead at the extremes.

**The only reliable way to establish causation is a controlled experiment.** For each Tier 1 recommendation, a follow-up A/B test would convert "association" into "evidence of causal impact." Until then, treat the recommendations as well-informed bets, not certainties.

---

## 6. Summary of Final Recommendation

| Action | Confidence | Risk |
|---|---|---|
| Improve inventory availability to 90%+ | High | Low |
| Drive footfall through location/event initiatives | High | Low (intent obvious, magnitude uncertain) |
| Sustain marketing, cap high spenders, reallocate to under-spent stores | Medium | Medium (diminishing returns observed) |
| Audit top over-performers and replicate practices | High | Low |
| Pause/test discount strategy | Medium | Low (current evidence is null) |
| Prioritise Airport/Mall over Residential for new stores | Medium | Medium (correlational, but evidence is strong) |
| Investigate Residential under-performance | High | Low |

For full model details: `outputs/model_equations.md`
For model comparison: `analysis/model_comparison.md`
For residual diagnostics: `analysis/residual_analysis.md`
