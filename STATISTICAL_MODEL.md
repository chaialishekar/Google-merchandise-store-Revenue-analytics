Statistical Model — Google Merchandise Store Revenue Analysis
This document explains the statistical methodology used to validate the funnel and revenue findings in this project. Implementation code lives in analysis.py (Phase 4 functions); this file explains the why and how to interpret behind each test.


1. Why Statistical Validation Was Needed
The initial SQL analysis showed a visual decline in conversion rate from December into January. A visual trend alone isn't proof of a real effect — daily conversion naturally fluctuates due to random noise. Three questions needed formal testing:

Is the December → January decline statistically real, or could it be normal day-to-day variance?
If real, is it isolated to one device/channel (suggesting a bug), or shared across segments (suggesting an external cause like seasonality)?
Can a simple trend line be trusted to forecast what happens next?

Each question maps to one statistical method below.


2. Welch's t-test — Is the Decline Real?
Method: Independent two-sample t-test (Welch's variant, which does not assume equal variance between groups — appropriate here since December's daily conversion values are more volatile than January's).

Formula:

t = \frac{\bar{X}_1 - \bar{X}_2}{\sqrt{\dfrac{s_1^2}{n_1} + \dfrac{s_2^2}{n_2}}}

Where:

X̄₁, X̄₂ = mean daily conversion rate for December and January
s₁², s₂² = variance of daily conversion rates within each month
n₁, n₂ = number of days in each month

Applied to this data:

December mean conversion: 1.73%
January mean conversion: 0.89%
Result: t = 4.332, p = 0.00006

Interpretation: A p-value this small means that if there were truly no difference between December and January (the null hypothesis), a gap this large would appear by random chance only about 6 times in 100,000. This comfortably clears the conventional significance threshold (p < 0.05), so the decline is treated as a real behavioral shift, not noise.

Assumptions checked:

Daily conversion rates are treated as independent observations within each month (reasonable at a daily grain)
Welch's variant was chosen specifically because it does not require equal variance between the two months, which a standard (Student's) t-test would assume


3. Pearson Correlation — Is It Seasonal or a Device-Specific Bug?
Method: Pearson correlation coefficient between desktop and mobile daily conversion rates.

Formula:

r = \frac{\sum (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum (x_i - \bar{x})^2 \sum (y_i - \bar{y})^2}}

Where x and y are the daily conversion rate series for two devices, and the result ranges from -1 (perfectly opposite) to +1 (perfectly together).

Applied to this data:

Result: r = 0.81 (desktop vs. mobile daily conversion rate)

Interpretation: A strong positive correlation means desktop and mobile conversion rose and fell together, day by day. If the January decline were caused by something specific to one device (e.g., a broken mobile checkout button), we would expect the two series to move independently — a low or negative correlation. Instead, they move together, which is far more consistent with a shared external cause (seasonality, broader market conditions) than a device-specific defect.

Caveat: Correlation shows the two series move together — it does not, by itself, prove why. It rules out a device-specific bug as the primary explanation but doesn't independently confirm seasonality; that inference also relies on the funnel-stage pattern (view-to-cart rate rising into December and falling after, matching a holiday shopping arc).


4. Simple Linear Regression — Forecasting, and Its Limits
Method: Ordinary least squares (OLS) linear regression of daily revenue against a day-index variable.

Formula:

\hat{y} = \beta_0 + \beta_1 x, \qquad \beta_1 = \frac{\sum (x_i-\bar{x})(y_i-\bar{y})}{\sum(x_i-\bar{x})^2}

Where x is the day number (0, 1, 2, ...) and ŷ is predicted daily revenue.

Applied to this data:

Result: β₁ (slope) = -44.27 revenue/day

Interpretation — and why this number should NOT be taken at face value: A naive reading says "revenue drops by ~$44 every day, indefinitely." This is misleading. The regression is fit across a window that includes a holiday demand spike (late November–December) followed by a normal post-holiday cooldown (January). A single straight line forced through a rise-then-fall pattern will always show a steep average downward slope for the second half, even if the business is simply returning to a normal, non-holiday baseline rather than genuinely deteriorating.

Why this matters methodologically: This is a deliberate example of using a simple model to check its own limitations, not just to produce a headline number. The correct takeaway documented in this project is: do not extrapolate this slope forward without accounting for seasonality — a model like SARIMA or Prophet with explicit seasonal terms would be needed for a trustworthy forecast, which is out of scope for this analysis but noted as a next step.


5. Supporting Business Formulas
These aren't hypothesis tests, but they're the metrics the statistical tests above were run on:

Conversion Rate

CR = \frac{\text{Purchasers}}{\text{Sessions}} \times 100

Average Order Value (AOV)

AOV = \frac{\text{Total Revenue}}{\text{Number of Transactions}}

Cart Abandonment Rate

CAR = 1 - \frac{\text{Purchased Users}}{\text{Added-to-Cart Users}}


6. Unit Economics — LTV, CAC, and Payback Period
Why this section exists: Revenue and conversion metrics explain what happened. Unit economics answer a different question — is each customer worth more than it costs to acquire them? This section is explicit about which numbers are real and which are illustrative, because this dataset contains no marketing spend or cost-per-channel data anywhere in its schema.
What can be computed from real data
Average Order Value (AOV) and Lifetime Value within the analysis window (LTV):

LTV_{\text{window}} = \frac{\text{Total Revenue}}{\text{Purchasing Customers}}

Applied to this data (from Q15_UNIT_ECONOMICS_INPUTS in analysis.py):

Purchasing customers: 4,066
Total orders: 5,242
Total revenue: $362,165
AOV = $69.09
Orders per customer = 1.29 (over the 3-month window)
LTV (3-month window) = $89.07 → $29.69/month per purchasing customer

These four numbers are real, computed directly from the same query family used throughout this project — no assumption involved.
What requires an assumption, and why
Customer Acquisition Cost (CAC):

CAC = \frac{\text{Total Acquisition Spend}}{\text{New Customers Acquired}}

This cannot be computed here — GA4 event data records behavior, not ad spend. Any CAC figure below is a labeled illustrative assumption, not a measured result.

LTV:CAC Ratio (industry rule of thumb: a healthy business is generally ≥ 3:1):

\frac{LTV}{CAC}

Payback Period (months to recover acquisition cost):

\text{Payback (months)} = \frac{CAC}{LTV_{\text{monthly}}}

Illustrative example (assumed CAC = $25 — chosen only to demonstrate the formula, not sourced from this data or any benchmark claim):

LTV:CAC ratio = 89.07 / 25 = 3.56 (above the 3:1 rule of thumb)
Payback period = 25 / 29.69 = 0.84 months (~26 days)

What would make this real instead of illustrative: joining this data against actual channel-level ad spend (e.g., Google Ads / Meta Ads cost reports) — outside the scope of what GA4 event data alone can provide. Q16_REVENUE_BY_USER_TYPE in analysis.py is provided as the next step: it segments LTV by new vs. returning customer, which is the correct pairing for a real CAC calculation once spend data is available, since CAC applies specifically to newly acquired customers.


7. Summary Table
Test
Question Answered
Result
Conclusion
Welch's t-test
Is the Dec→Jan decline real?
t = 4.332, p = 0.00006
Yes — statistically significant
Pearson correlation
Is it device-specific or shared?
r = 0.81
Shared across desktop/mobile — supports seasonality
Linear regression
Can we trust a simple forecast?
slope = -44.27/day
No — overstates decline; seasonality-aware model needed
Unit economics
Is the customer worth acquiring?
LTV = $89.07 (real); CAC illustrative only
LTV:CAC and payback shown as a labeled example, not a measured result




Implementation of these tests: see run_ttest_dec_vs_jan(), run_device_correlation(), and run_revenue_forecast() in analysis.py.

