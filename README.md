# Google Merchandise Store: Diagnosing a Revenue Decline

*A business analytics case study using SQL, Python, Tableau, statistical analysis, and product thinking.*

---

# Business Problem

The Google Merchandise Store experienced a decline in revenue despite continued customer activity.

At first glance, it was unclear **why revenue was falling**. Was the business attracting fewer visitors? Were customers abandoning their carts? Was the issue seasonal, behavioral, or something else entirely?

Instead of jumping to conclusions, this project approaches the problem the way a Business Analyst or Product Analyst would—by investigating the complete customer journey, validating findings with statistics, and transforming insights into a product recommendation.

<p align="center">
<img src="assets/story/process.png" width="850">
</p>

The investigation follows six stages:

1. Understand the business problem.
2. Follow the customer journey.
3. Identify where customers drop off.
4. Validate the findings statistically.
5. Design a product solution.
6. Measure the expected business impact.

---

# The Investigation

Every analytics project starts with a question.

In this case, the question wasn't simply:

> **"Why is revenue declining?"**

It became:

> **"Where exactly is the business losing customers, and what can we build to change that?"**

To answer this, the project was broken into a series of business investigations.

## Business Questions

- Is the decline caused by lower website traffic or weaker conversion?
- Which stage of the purchase funnel loses the most customers?
- Are the observed changes statistically significant?
- Which customer segments contribute most to revenue loss?
- How can a product improvement increase completed purchases?

---

# Methodology

The investigation combines business analytics with statistical validation.

### Data Exploration

- Understand customer sessions
- Analyze transaction behavior
- Explore revenue trends

### Funnel Analysis

- Session → Product View
- Product View → Add to Cart
- Add to Cart → Checkout
- Checkout → Purchase

### Statistical Validation

- Welch's t-test
- Pearson Correlation
- Linear Regression

### Product Thinking

Instead of stopping at the analysis, the findings are translated into a product proposal designed to reduce customer friction and improve conversion.

---

# Key Findings

- Revenue decline was driven primarily by **conversion**, not acquisition.
- The largest customer drop-off occurred **before checkout**.
- Statistical testing confirmed that the decline was significant.
- Customer behavior patterns suggested friction in the purchase journey.
- A guided product discovery experience was proposed to improve conversion.

---

# Repository Contents

| File | Description |
|------|-------------|
| [`analysis.py`](analysis.py) | Complete SQL and Python analysis |
| [`Story.html`](Story.html) | Interactive project walkthrough |
| `Presentation.pdf` | Executive presentation |
| `Product_Proposal.pdf` | Product recommendation |
| `Statistical_Analysis.pdf` | Statistical appendix |
| `Dashboard.twbx` | Tableau workbook |

---

# Deliverables

| Resource | Link |
|----------|------|
| Interactive Tableau Dashboard | [View Dashboard](https://public.tableau.com/views/G4RevenueAnalytics/Dashboard2?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link) |
| Interactive Story | [Open Story](Story.html) |
| Executive Presentation | Coming Soon |
| Product Proposal | Coming Soon |
| Statistical Analysis | Coming Soon |

---

# Technologies

- Google BigQuery
- SQL
- Python
- pandas
- SciPy
- scikit-learn
- Tableau
- Figma

---

# Dataset

Google Analytics 4 Sample E-commerce Dataset

```
bigquery-public-data.ga4_obfuscated_sample_ecommerce
```

---

# Run the Project

```bash
pip install -r requirements.txt
python analysis.py
```

---

# Skills Demonstrated

- Business Analysis
- Product Analytics
- Revenue Analytics
- Funnel Analysis
- Statistical Testing
- SQL
- Python
- Data Storytelling
- Product Strategy