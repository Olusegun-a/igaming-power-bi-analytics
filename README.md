
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

## Python Pre-Processing Summary

Python was used to clean and standardise the raw CSV files before loading them into Power BI.

### Work Completed

- Loaded the three source files into Pandas.
- Standardised all date columns into a consistent format.
- Corrected mixed day/month date issues in `trackAff_feed`.
- Fixed incorrect negative commission values in `affMatrix_feed`.
- Parsed `tracking_id` to create campaign and card keys.
- Converted all commission values to GBP using assumed FX rates.
- Exported cleaned files for Power BI modelling.

### Output Files

- `trackAff_feed_clean.csv`
- `affMatrix_feed_clean.csv`
- `campaign_data_clean.csv`
  
## Load and Transform Preprocessed Data

### Load Source Tables in Power BI

Load the following three tables into **Power BI** using **Power Query**:

- `affMatrix_feed`
- `trackaff_feed`
- `campaign`

Standardize the `tracking_id` column in all relevant tables to **Text** data type.

### Standardize Date Formats

Convert all date columns across the three tables to `DD-MM-YYYY`.

## Table Transformations

### `affMatrix_feed`

- The `commission_earned` column contains incorrect negative values (`-300`) instead of positive values (`300`) after `ftd_date` confirmation.
- Replace all values of `-300` with `300`.
- `player_id` is missing in **6 rows**.
- Create a new `campaign_id` column using `RIGHT(tracking_id, 1)`.

### `trackaff_feed`

- Create a new `campaign_id` column using `RIGHT(card_id, 1)`.
- The `date` column contains incorrect records where the **day and month are swapped** in some entries.
- Correct the dates to `DD-MM-YYYY`.
- Use a **Custom Column** in Power Query to switch `DD` and `MM` where the day is `3` and the month is greater than `3`.
- **82 rows** were fixed.

### `campaign`

- Clean the `campaign_name` column by removing underscores (`_`) using **Replace Values**.

## Data Quality Note

### Funnel Validation Issue

For some campaigns, **registrations exceed recorded clicks**.

This reflects a data quality issue because, logically, registrations should be less than or equal to clicks in the funnel journey:

`Clicks >= Registrations`

## 2. Data Modelling

A star schema was used by separating split descriptive fields into dimension tables and keeping event metrics in fact tables.

### Dimension Tables Created

- **Dim_Date**  
  Created to provide a central calendar table and link date fields across all three source tables.

- **Dim_Campaign**  
  Created using unique campaign IDs and campaign names to connect campaign performance across all tables.

- **Dim_Operator**  
  Created using unique operator names to unify operator reporting across affiliate sources.

### Fact Tables

- `campaign_data_clean`
- `trackAff_feed_clean`
- `affMatrix_feed_clean`

### Relationships

The dimension tables were used in Model View to create one-to-many relationships into the fact tables:

Dim_Date      → all fact tables
Dim_Campaign  → all fact tables
Dim_Operator  → affiliate fact tables


### 3. Analysis — DAX Measures

DAX measures were created to answer the business questions.

---

### Which operator generated the most total commission over the quarter?

**Answer:** `LuckyEdge`

Total Earn Commission GBP =
SUM(trackAff_feed_clean[total_commission_gbp]) +
SUM(affMatrix_feed_clean[commission_earned_gbp])

### Which campaign delivered the best return on spend (commission earned vs spend)?

Return on Ad Spend (ROAS) =
DIVIDE([Total Commission Revenue (£)], [Marketing Spend (£)])

### What is the click-to-registration ratio and the registration-to-FTD ratio, by operator and by campaign?

Total Registrations =
SUM(trackAff_feed_clean[registrations]) +
COUNTROWS(affMatrix_feed_clean)

### Which card performed best within each campaign?

Which card performed best within each campaign?

### Week-on-week and month-on-month commission change — for the business as a whole and by operator

Previous Week Commission
WoW Commission Change GBP
WoW Commission Change %
Previous Month Commission
MoM Commission Change GBP
MoM Commission Change %

### Are there any anomalies or data quality issues the business should be aware of?

The following issues were identified and flagged in the report:

Registrations exceeding clicks in some campaign views
Mixed date formatting requiring correction
Missing player_id values in affMatrix_feed
Incorrect negative commission values in affMatrix_feed
Partial final week impacting WoW comparisons

## 4. Dashboard

A two-page Power BI report was designed for non-technical commercial stakeholders.

The dashboard was structured to allow users to quickly understand overall business performance, compare campaign efficiency, and review operator conversion trends.

---

## Page 1 — Executive Overview

This page provides a high-level summary of Q1 2025 performance.

### Visuals Included

- **Total Commission KPI Card**
  - Displays total commission revenue generated during Q1.

- **Total Commission by Month Chart**
  - Monthly trend across January, February, and March.
  - Helps stakeholders identify growth or decline across the quarter.
---

## Page 2 — Commercial Performance Breakdown

This page focuses on campaign efficiency and operator performance.

### Visuals Included

- **Campaign ROAS Comparison Chart**
  - Compares Return on Ad Spend across all campaigns.
  - Helps identify the most efficient campaign.

- **Operator Funnel Performance Visual**
  - Registrations
  - First Time Deposits (FTDs)

- **Dynamic Ranking Chart**
  - Built using a `RANKX()` DAX measure.
  - Allows users to rank operators or campaigns based on selected KPIs.

---

## Dynamic Elements Used

The dashboard includes interactive features such as:

- Dynamic ranking visual using `RANKX()`

---

## Outcome

The final dashboard enables stakeholders to:

- Monitor performance
- Compare campaign ROI
- Review operator funnel efficiency
- Make quicker commercial decisions using trusted data
