# Statistical Model — Google Merchandise Store Revenue Analysis

This document explains the statistical methodology used to validate the funnel and revenue findings in this project. Implementation code lives in [`analysis.py`](../analysis.py) (Phase 4 functions); this file explains the *why* and *how to interpret* behind each test.

---

## 1. Why Statistical Validation Was Needed

The initial SQL analysis showed a visual decline in conversion rate from December into January. A visual trend alone isn't proof of a real effect — daily conversion naturally fluctuates due to random noise. Three questions needed formal testing:

1. Is the December → January decline **statistically real**, or could it be normal day-to-day variance?
2. If real, is it **isolated to one device/channel** (suggesting a bug), or **shared across segments** (suggesting an external cause like seasonality)?
3. Can a simple trend line be trusted to **forecast** what happens next?

Each question maps to one statistical method below.

---

## 2. Welch's t-test — Is the Decline Real?

**Method:** Independent two-sample t-test (Welch's variant, which does not assume equal variance between groups — appropriate here since December's daily conversion values are more volatile than January's).

**Formula:**

```math
t = \frac{\bar{X}_1 - \bar{X}_2}{\sqrt{\dfrac{s_1^2}{n_1} + \dfrac{s_2^2}{n_2}}}
```

Where:
- X̄₁, X̄₂ = mean daily conversion rate for December and January
- s₁², s₂² = variance of daily conversion rates within each month
- n₁, n₂ = number of days in each month

**Applied to this data:**
- December mean conversion: 1.73%
- January mean conversion: 0.89%
- **Result: t = 4.332, p = 0.00006**

**Interpretation:** A p-value this small means that if there were truly no difference between December and January (the null hypothesis), a gap this large would appear by random chance only about 6 times in 100,000. This comfortably clears the conventional significance threshold (p < 0.05), so the decline is treated as a real behavioral shift, not noise.

**Assumptions checked:**
- Daily conversion rates are treated as independent observations within each month (reasonable at a daily grain)
- Welch's variant was chosen specifically because it does not require equal variance between the two months, which a standard (Student's) t-test would assume

---

## 3. Pearson Correlation — Is It Seasonal or a Device-Specific Bug?

**Method:** Pearson correlation coefficient between desktop and mobile daily conversion rates.

**Formula:**

```math
r = \frac{\sum (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum (x_i - \bar{x})^2 \sum (y_i - \bar{y})^2}}
```

Where x and y are the daily conversion rate series for two devices, and the result ranges from -1 (perfectly opposite) to +1 (perfectly together).

**Applied to this data:**
- **Result: r = 0.81** (desktop vs. mobile daily conversion rate)

**Interpretation:** A strong positive correlation means desktop and mobile conversion rose and fell together, day by day. If the January decline were caused by something specific to one device (e.g., a broken mobile checkout button), we would expect the two series to move independently — a low or negative correlation. Instead, they move together, which is far more consistent with a shared external cause (seasonality, broader market conditions) than a device-specific defect.

**Caveat:** Correlation shows the two series move together — it does not, by itself, prove *why*. It rules out a device-specific bug as the primary explanation but doesn't independently confirm seasonality; that inference also relies on the funnel-stage pattern (view-to-cart rate rising into December and falling after, matching a holiday shopping arc).

---

## 4. Simple Linear Regression — Forecasting, and Its Limits

**Method:** Ordinary least squares (OLS) linear regression of daily revenue against a day-index variable.

**Formula:**

```math
\hat{y} = \beta_0 + \beta_1 x, \qquad \beta_1 = \frac{\sum (x_i-\bar{x})(y_i-\bar{y})}{\sum(x_i-\bar{x})^2}
```

Where x is the day number (0, 1, 2, ...) and ŷ is predicted daily revenue.

**Applied to this data:**
- **Result: β₁ (slope) = -44.27 revenue/day**

**Interpretation — and why this number should NOT be taken at face value:** A naive reading says "revenue drops by ~$44 every day, indefinitely." This is misleading. The regression is fit across a window that includes a holiday demand spike (late November–December) followed by a normal post-holiday cooldown (January). A single straight line forced through a rise-then-fall pattern will always show a steep average downward slope for the second half, even if the business is simply returning to a normal, non-holiday baseline rather than genuinely deteriorating.

**Why this matters methodologically:** This is a deliberate example of using a simple model to check its own limitations, not just to produce a headline number. The correct takeaway documented in this project is: *do not extrapolate this slope forward without accounting for seasonality* — a model like SARIMA or Prophet with explicit seasonal terms would be needed for a trustworthy forecast, which is out of scope for this analysis but noted as a next step.

---

## 5. Supporting Business Formulas

These aren't hypothesis tests, but they're the metrics the statistical tests above were run on:

**Conversion Rate**
```math
CR = \frac{\text{Purchasers}}{\text{Sessions}} \times 100
```

**Average Order Value (AOV)**
```math
AOV = \frac{\text{Total Revenue}}{\text{Number of Transactions}}
```

**Cart Abandonment Rate**
```math
CAR = 1 - \frac{\text{Purchased Users}}{\text{Added-to-Cart Users}}
```

---

## 6. Summary Table

| Test | Question Answered | Result | Conclusion |
|---|---|---|---|
| Welch's t-test | Is the Dec→Jan decline real? | t = 4.332, p = 0.00006 | Yes — statistically significant |
| Pearson correlation | Is it device-specific or shared? | r = 0.81 | Shared across desktop/mobile — supports seasonality |
| Linear regression | Can we trust a simple forecast? | slope = -44.27/day | No — overstates decline; seasonality-aware model needed |

---

*Implementation of these tests: see `run_ttest_dec_vs_jan()`, `run_device_correlation()`, and `run_revenue_forecast()` in [`analysis.py`](../analysis.py).*
