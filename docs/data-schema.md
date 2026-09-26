# Data Schema

## Review_Input

| Field | Description |
|---|---|
| review_id | Unique review identifier |
| asin | Product identifier |
| product_name | Product name |
| rating | Rating from 1 to 5 |
| review_title | Review headline |
| review_text | Full review content |
| review_date | Review date |
| verified_purchase | Whether the purchase was verified |
| helpful_votes | Helpful vote count |
| processing_status | Use `NEW` or `ANALYZED` |

## Review_Analysis

| Field | Description |
|---|---|
| review_id | Source review identifier |
| sentiment | positive, negative, neutral, or mixed |
| sentiment_score | Normalized sentiment score |
| primary_theme | Main customer theme |
| customer_issue | Main complaint or issue |
| positive_signal | Positive customer evidence |
| desired_improvement | Requested improvement |
| severity | low, medium, high, or critical |
| responsible_team | Team best placed to act |
| listing_opportunity | Listing improvement flag |
| creative_opportunity | Creative opportunity flag |
| recommended_action | Suggested business response |
| confidence | Model confidence |
| priority_score | Rules-based urgency score |
| priority_level | LOW, MEDIUM, HIGH, or CRITICAL |
| requires_alert | Whether an immediate alert is required |
| analyzed_at | Analysis timestamp |

## System_Summary

Stores aggregate review counts, sentiment distribution, priority counts, alert totals, rating averages, score averages, and top themes for reporting.
