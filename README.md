<img width="106" height="25" alt="image" src="https://github.com/user-attachments/assets/d12fb16f-f6ed-461f-aa8c-16eeed924873" />

# iGaming-Power-BI-Analytics
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
| date | DATE | NA | Date of campaign activity | Dim_Date[Date] | Standardised to date format in Power Query / Python |
| campaign_id | INT | NA | Unique id for each campaign | Dim_Campaign[campaign_id], parsed campaign_id in affiliate tables | Use as primary campaign key |
| campaign_name | TEXT | NA | campaign name for reporting | `Dim_Campaign[campaign_name]` | Store in dimension table |
| impressions | INT | NA  | Number of ad views | Fact only | |
| clicks | INT | NA | Number of ad clicks | Used in funnel analysis | |
| inbounds | INT | NA | Users landing on company's page | Fact only |  |
| outbounds | INT | NA | Users sent to operator platform | Used with affiliate conversions | NA |
| spend_gbp | DECIMAL | NA | Campaign marketing cost in GBP | ROI measures with commission facts | na |

---


