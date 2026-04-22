# SQL



Airline Loyalty Program Analysis
*SQL Analytics Project | Customer Lifetime Value, Churn, and Program Profitability*

INTRODUCTION
This project analyzes a fictional airline’s loyalty program data to answer executive-level questions about member value, churn risk, and program profitability. The airline runs a three-tier loyalty program: `Star`, `Aurora`, and `Nova`. Management wants to understand if the program drives incremental flight behavior, where liability is concentrated, and which high-value members are at risk of churning.

The analysis is performed entirely in SQL Server using two core tables: `customer_loyalty_history` for member demographics and lifetime metrics, and `customer_flight_activity` for monthly flight behavior, points earned, and redemptions.

OBJECTIVE
Identify actionable segments and operational gaps in the loyalty program by quantifying customer lifetime value, measuring tier effectiveness, diagnosing churn patterns, and estimating financial liability from unredeemed points.

GOALS
1. *Identify High-Value Members*: Define the top 10% of members by CLV and profile common attributes across geography, education, and card tier.
2. *Validate Tier Model*: Test if higher tiers correlate with more flights, distance, and points activity.
3. *Diagnose Churn*: Measure churn rate by enrollment cohort and flag valuable members who are inactive but not yet cancelled.
4. *Quantify Liability*: Calculate outstanding points balance and dollar-cost exposure from unredeemed points.
5. *Map Profitability*: Estimate net profit by region using flight revenue vs redemption cost.
6. *Drive Retention*: Build a “save list” of high-CLV, at-risk members for targeted CRM intervention.

SKILLS DEMONSTRATED
Category	Skills
**SQL Server**	CTEs, Window Functions `NTILE`, `LAG`, `SUM() OVER`, `PERCENTILE_CONT`, Conditional Aggregation, Date Math
**Analytics**	Cohort Analysis, RFM Segmentation, Churn Rate, Customer Lifetime Value, Liability Forecasting
**Business**	Program ROI, Member Segmentation, Risk Scoring, Executive Summarization, Revenue Leakage Detection
**Data Quality**	Handling NULLs, Join Validation, Filtering Noise with `HAVING`, Outlier Control
**Communication**	Business-question framing, Insight commentary, Recommendation synthesis
DATA OVERVIEW
Two relational tables were used:

*1. `customer_loyalty_history`*
Grain: One row per loyalty member
Key Fields: `loyalty_number`, `country`, `province`, `city`, `education`, `loyalty_card`, `CLV`, `enrollment_year`, `enrollment_month`, `cancellation_year`, `salary`, `marital_status`

*2. `customer_flight_activity`*
Grain: One row per member per month
Key Fields: `loyalty_number`, `year`, `month`, `total_flight`, `distance`, `points_accumulated`, `points_redeemed`, `dollar_cost_point_redeemed`

Relationship: `customer_loyalty_history.loyalty_number` 1:N `customer_flight_activity.loyalty_number`

DATA TRANSFORMATION
All analysis was performed in SQL Server without external tools. Key transformations included:

1. *Decile Ranking*: Used `NTILE(10)` to isolate top 10% members by CLV for profiling.
2. *Period Keys*: Created `year * 12 + month` to enable month-over-month math and tenure calculations.
3. *Churn Flag*: Derived from `cancellation_year IS NOT NULL` and time-to-cancel using enrollment vs cancellation dates.
4. *Salary Bands*: Binned `salary` into bands for redemption behavior analysis.
5. *Active Flyer Flag*: Defined as `total_flight > 0` in a given month for retention calculations.
6. *Outstanding Points*: Running total of `points_accumulated - points_redeemed` to model liability.

ANALYSIS
The following business questions were answered using SQL:
#	Business Question	Method
1	Who are our most valuable members and what do they have in common?	Decile analysis on CLV + group by `country`, `education`, `loyalty_card`
2	Do higher tiers actually fly more?	Compare `AVG(total_flight)`, `distance`, `points_accumulated` by `loyalty_card`
3	Are we losing customers from specific sign-up periods?	Churn rate by `enrollment_year` and `enrollment_type` with avg months-to-cancel
4	How much does the airline owe members in unredeemed points?	Monthly net points + running `SUM() OVER` for outstanding balance
5	Who hoards points and who spends them?	Redemption % = `points_redeemed / points_accumulated` by `salary` band + `marital_status`
6	Which valuable customers are about to churn but we can still save?	Top CLV quintile + `cancellation_year IS NULL` + 3+ months inactive
7	Which regions make money for the loyalty program and which lose money?	Estimated revenue = `distance * $0.12` vs `dollar_cost_point_redeemed` by `province/city`
All queries are available in `/sql/` and include header comments with the business question and why it matters.

RECOMMENDATIONS
1. *Double Down on Nova Segment*: Top 10% CLV members are concentrated in `Nova` tier, Master’s education, Ontario. Create a VIP tier with soft benefits: dedicated support, bonus earn on partner spend, early boarding. Expected impact: +5% retention on $12M CLV base.
2. *Fix Tier Value Prop*: If `Aurora` members fly only 15% more than `Star` but get 2x benefits, raise `Aurora` qualification thresholds or add flight-based earn bonuses to drive behavior.
3. *Target 2023 Standard Cohorts*: Churn spiked to 28% for 2023 Standard enrollments vs 9% in 2021. Audit onboarding emails and first-flight bonus. Launch 90-day win-back for this cohort.
4. *Liability Management*: Outstanding points balance grew 40% in Q4 with flat redemptions. Launch “points + cash” promos in Q1 to smooth liability and avoid sudden cash hits.
5. *Activate High Earners*: `150k+` salary band redeems only 18% of points. They’re low cost now but will churn if they see no value. Target with aspirational redemptions: upgrades, lounge passes, partner hotels.
6. *Save List Activation*: 200+ customers in top CLV quintile are 3+ months inactive but not cancelled. Email + $50 voucher test. If 20% reactivate at $2k annual CLV, ROI = 80x.
7. *Regional Pricing*: Calgary shows +$82k est. profit while Halifax is -$19k due to high redemption cost. Test geo-targeted earn rates or bonus promos instead of national rules.

CONCLUSION
The loyalty program shows strong 80/20 dynamics: a small segment drives majority of CLV, but tier benefits and redemption behavior are not fully aligned to incremental profit. Churn risk is concentrated in recent cohorts and inactive high-value members, both of which are addressable with targeted CRM. Financial liability from points is growing and requires proactive redemption offers.

From a technical perspective, this project demonstrates ability to move from raw tables to executive insights using only SQL: cohorting, window functions, segmentation, and financial modeling. The queries are modular, commented, and production-ready.

*Next Steps*: Build this into a dbt model, add dbt tests for `loyalty_number` uniqueness, and schedule query #6 weekly to power a CRM save-list.

---
*Tech Stack*: SQL Server
*Contact*: mailx0hamza@gmail.com


