<img width="106" height="25" alt="image" src="https://github.com/user-attachments/assets/d12fb16f-f6ed-461f-aa8c-16eeed924873" /># iGaming-Power-BI-Analytics
End-to-end Power BI solution with data cleaning, star schema modelling, and DAX analysis of multi-source affiliate marketing data.

## PeakMedia iGaming Marketing Analytics

### Business Objectives

This project focuses on analysing marketing performance for PeakMedia, an iGaming affiliate marketing company. The company drives traffic to online casino and sportsbook operators through performance marketing campaigns.

The objective of this analysis is to evaluate the effectiveness of campaigns in converting traffic into registrations, first-time deposits (FTDs), and commission revenue.

The data provided for this project requires cleaning, transformation, and modelling before insights can be generated in Power BI.

---

### Data Tables

#### Campaign Data

| Column Name | Dtype | Data Issue | Purpose | Links To | Mitigation |
|------------|------|-----------|---------|----------|------------|
| date | DATE | None identified | Date of campaign activity | `Dim_Date[Date]` | Standardised to date format in Power Query / Python |
| campaign_id | INT | None identified | Unique identifier for each campaign | `Dim_Campaign[campaign_id]`, parsed campaign_id in affiliate tables | Use as primary campaign key |
| campaign_name | TEXT | Potential naming inconsistencies over time | Human-readable campaign name for reporting | `Dim_Campaign[campaign_name]` | Store in dimension table as master label |
| impressions | INT | Possible nulls / zero values | Number of ad views | Fact only | Replace nulls with 0 if valid |
| clicks | INT | Could exceed outbounds logic checks needed | Number of ad clicks | Used in funnel analysis | Validate clicks ≤ impressions |
| inbounds | INT | Definition may vary | Users landing on owned site/page | Fact only | Confirm business definition in README |
| outbounds | INT | May exceed clicks if tracking issue exists | Users sent to operator sites | Used with affiliate conversions | Flag if outbounds > clicks |
| spend_gbp | DECIMAL | Negative / null values possible | Campaign marketing cost in GBP | ROI measures with commission facts | Replace nulls, flag negatives |

---


