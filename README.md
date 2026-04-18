
# iGaming-Power-BI-Analytics
End-to-end Power BI solution with data cleaning, star schema modelling, and DAX analysis of multi-source affiliate marketing data.

## PeakMedia iGaming Marketing Analytics

### Business Objectives

This project focuses on analysing marketing performance for PeakMedia, an iGaming affiliate marketing company. The company drives traffic to online casino and sportsbook operators through performance marketing campaigns.

The objective of this analysis is to evaluate the effectiveness of campaigns in converting traffic into registrations, first-time deposits (FTDs), and commission revenue.

The data provided for this project requires cleaning, transformation, and modelling before insights can be generated in Power BI.

---

### Original Data Tables

#### Campaign Data

| Column Name | Dtype | Data Issue | Purpose | Links To | Mitigation |
|------------|------|-----------|---------|----------|------------|
| date | DATE | NA | Date of campaign activity | Dim_Date[Date] | Standardised to date format in Power Query / Python |
| campaign_id | INT | NA | Unique id for each campaign | Dim_Campaign[campaign_id], parsed campaign_id in affiliate tables | Use as primary campaign key |
| campaign_name | TEXT | NA | campaign name for reporting | `Dim_Campaign[campaign_name]` | Store in dimension table |
| impressions | INT | NA  | Number of ad views | Fact only | |
| clicks | INT | NA | Number of ad clicks | Used in funnel analysis | |
| inbounds | INT | NA | Users landing on company's page | Fact only |  |
| outbounds | INT | NA | Users sent to operator platform | Used with affiliate conversions | NA |
| spend_gbp | DECIMAL | NA | Campaign marketing cost in GBP | ROI measures with commission facts | na |

---


## trackAff Feed

| Column Name | Dtype | Data Issue | Purpose | Links To | Mitigation |
|------------|------|-----------|---------|----------|------------|
| date | DATE | mixed formats | Daily activity date | `Dim_Date[Date]` | standardised format to dd-mm-yyyy |
| operator_name | TEXT | NA | Operator receiving traffic | `Dim_Operator[operator_name]` | NA |
| tracking_id | INT | some rows missing tracking_ids | ID linking campaign/card/date | Used to derive campaign/card/date | use campaign/card/date where tracking_id is missing|
| card_id | INT | NA | card identifier | `Dim_Card[card_id]` | Validate with tracking_id |
| registrations | INT | NA | Number of player registrations | Funnel metrics | NA |
| ftds | INT | NA | First-time deposits | Funnel metrics | NA |
| commission_per_ftd | INT | Currency-dependent | Revenue earned per FTD | Used in commission calculation | Convert to GBP equivalent |
| total_commission | INT | Mixed currencies | Total commission earned | ROI Metric | Create `total_commission_gbp` column |
| currency | TEXT | NA | currency code, use to standardize commission output |  | NA |

---

## affMatrix Feed

| Column Name | Dtype | Data Issue | Purpose | Links To | Mitigation |
|------------|------|-----------|---------|----------|------------|
| player_id | TEXT | NA | Unique player record | Fact only | NA |
| operator_name | TEXT | NA | Operator associated to player | `Dim_Operator[operator_name]` | NA |
| product | TEXT | Missing values | Casino / Sportsbook product type |  |  |
| tracking_id | INT |  | ID linking campaign/card/date | Used to derive campaign/card/date |  |
| card_id | INT | NA | card identifier | `Dim_Card[card_id]` | Validate with tracking_id |
| registration_date | DATE | NA | Date player registered | `Dim_Date[Date]` |  |
| ftd_date | DATE | Blank if no deposit | Date of first deposit | `Dim_Date[Date]` |  |
| commission_per_ftd | DECIMAL | Mixed currencies | Commission rate per FTD | Revenue / roi | Convert to GBP |
| commission_earned | DECIMAL |   | Actual commission earned | Revenue / roi | Create GBP equivalent |
| currency | TEXT | Multiple currencies | Original currency code |   |   |

---

## Data Preprocessing in Python
