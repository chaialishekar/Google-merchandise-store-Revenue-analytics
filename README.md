# Google Merchandise Store: Diagnosing a Revenue Decline

*A business analytics case study using SQL, Python, Tableau, and product strategy.*

## Business Problem

Revenue is declining at the Google Merchandise Store, but traffic appears stable. This project investigates whether the decline is caused by lower acquisition, weaker conversion, customer behavior, or seasonality, and translates the findings into a product recommendation.

## Business Questions

1. Is revenue loss driven by traffic or conversion?
2. Where do customers abandon the purchase funnel?
3. Is the decline statistically significant?
4. What product solution would improve business outcomes?

## Executive Summary

- 267,116 GA4 sessions analyzed
- Revenue decline was conversion-driven
- Largest funnel leak occurred before checkout
- Welch's t-test: p = 0.00006
- Desktop/Mobile correlation: r = 0.81
- Recommendation: Guided product-discovery experience

## Repository Contents

| File | Description |
|------|-------------|
| analysis.py | Complete SQL + Python analysis |
| Story.html | Interactive visual explanation |
| Presentation.pptx | Executive presentation (add) |
| Product_Proposal.pdf | Product solution (add) |
| Statistical_Analysis.pdf | Statistical appendix (add) |
| Dashboard.twbx | Tableau workbook (optional) |

## Story

Story images are available under `assets/story/`.

## Dashboard

Add your Tableau Public link here.

## Technologies

Google BigQuery • SQL • Python • pandas • SciPy • scikit-learn • Tableau • Figma

## Dataset

`bigquery-public-data.ga4_obfuscated_sample_ecommerce`

## Run

```bash
pip install -r requirements.txt
python analysis.py
```
