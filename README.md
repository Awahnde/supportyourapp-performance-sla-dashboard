# SupportYourApp Performance SLA Case Study
<p align="center">
  <img src="https://supportyourapp.com/img/support-your-app-logo.svg" alt="Support Your App Logo" width="800">
</p>

## Table of Contents

* [Company Overview](#company-overview)

* [Project Overview](#property-overview) 

* [Data Sources](#data-sources)

* [Tools](#tools)

* [Data Understanding & Structure](#data-understanding-&-structure)

* [Data Preparation](#data-preparation)

* [Exploratory Data Analysis](#exploratory-data-analysis)

* [Data Analysis](data-analysis)

* [Results](results)

* [Recommendations](recommendations)

* [References](references)

## Company Overview

SupportYourApp is a multilingual customer support outsourcing company that provides managed support services to software and technology businesses. Their clients span SaaS platforms across industries including e-commerce, financial technology, and health technology. Support is delivered across multiple regions: North America (NA), Europe, Middle East & Africa (EMEA), and Asia-Pacific (APAC) and in several languages including English, French, and Spanish.

The company's core value proposition is delivering fast, consistent, and SLA-compliant support on behalf of their clients. This makes performance tracking against SLA targets and CSAT benchmarks central to how they demonstrate value and identify operational risk.

## Project Overview
This project builds an Excel-based performance and SLA monitoring dashboard for SupportYourApp's customer support operations.

The goal was to give operations and client success teams a clear view of where support performance stands — both in aggregate and broken down by the dimensions that actually matter operationally: which agents are carrying the most load, which clients are at SLA risk, where response times are slowest, and whether issue types are being resolved efficiently.

The dashboard is organized across four analytical views:


* Regional & Language Performance: Response time and SLA compliance by region and support language
* Client & SLA Risk Analysis: SLA compliance rates and resolution patterns per client
* Issue & Process Efficiency: Ticket volume, escalation patterns, and resolution time by issue type
* Agent Performance & Workload: Per-agent ticket counts, response times, and SLA compliance


The project covers the full analytical workflow: data understanding, cleaning, pivot-based analysis, and dashboard design with slicers for interactive filtering.

![Support_Your_App_dashboard](https://github.com/user-attachments/assets/3835c652-c801-4685-a340-bedae590ca07)

## Data Sources

This project uses a simulated, synthetic dataset created for portfolio and learning purposes. The data does not represent real clients, users, or operational records and was generated to reflect realistic customer support scenarios for analytical demonstration only.

The dataset used for this project is a single-table operational support log containing 150 ticket records from October 2024. Each row represents one resolved support ticket.

Key fields in the dataset:

## Dataset Fields

| Field | Description |
|---------|-------------|
| `Ticket_ID` | Unique identifier per ticket |
| `Agent_Name` | Support agent assigned to the ticket |
| `Client` | Client whose end-user raised the ticket (E-commerce App, FinTech SaaS, HealthTech Platform) |
| `Region` | Geographic region of the ticket (APAC, EMEA, NA) |
| `Language` | Language the ticket was handled in (English, French, Spanish) |
| `Issue_Type` | Category of the support issue |
| `Priority` | Ticket priority level (High, Medium, Low) |
| `First_Response_Time_mins` | Time in minutes from ticket creation to first agent response |
| `Resolution_Time_hours` | Time in hours from creation to ticket close |
| `SLA_Compliance` | Binary Yes/No — whether the ticket was resolved within the agreed SLA window |
| `CSAT_Score` | Customer satisfaction score (scale of 1–5) |
| `Escalated` | Whether the ticket was escalated |

## Tools

Microsoft Excel - data cleaning, pivot tables, calculated fields, and dashboard design

PivotTables for aggregations by agent, region, client, and issue type
Slicers for interactive filtering across dashboard views
Conditional formatting to highlight SLA risk and performance thresholds
Named ranges and structured references for cleaner formula logic

## Data Understanding & Structure

Before jumping into pivot tables, I spent time understanding what the dataset was actually telling me and where the analytical gaps might be.

Shape: 150 rows × 12 columns. One row per resolved ticket, all from October 2024.

Three clients, three regions, three languages. This meant any slice I built could be cross-referenced: for example, SLA compliance by region vs. by client vs. by language. That structure suggested the dashboard should let users filter across these dimensions rather than hard-code a single view.

SLA Compliance is binary. The SLA_Compliance field is Yes/No, not a time-to-breach value. This meant I couldn't calculate how close tickets came to breaching, only whether they did. I flagged this as a limitation when interpreting results.

Priority distribution was uneven across clients. High-priority tickets were not evenly distributed, which mattered for interpreting per-agent SLA rates. An agent handling more high-priority tickets will naturally have different response metrics than one handling mostly low-priority work.

One month of data. All 150 tickets come from October 2024, so any trends are within-month patterns rather than longitudinal analysis. I kept this in mind when framing findings, avoiding language like "declining performance" when I could only see a single snapshot.

Potential data quality issue with "Unknown" client. A small number of tickets had Unknown as the client value. Rather than dropping these records, I kept them in and flagged them separately in the SLA risk analysis view, since they could indicate a tagging issue in the source system worth investigating.

## Data Preparation

The dataset arrived in reasonably clean condition, but I made several decisions before building analysis:

1. Standardized column headers. A few column names had inconsistent spacing or formatting. I standardized these to ensure PivotTable field names were clean and readable in slicers.

2. Checked for blanks and duplicates. I ran a quick duplicate check on Ticket_ID — all 150 were unique. Blank values were limited to the Unknown client grouping noted above.

3. Created a SLA_Compliant_Flag helper column. The original SLA_Compliance field was text (Yes/No). I added a numeric flag (1/0) to enable straightforward SLA compliance rate calculations in PivotTables using AVERAGE()
   rather than COUNTIF() logic — cleaner and less error-prone when slicers are applied.

4. Formatted Resolution_Time_hours for readability. Raw values were decimals (e.g., 15.85 hours). I kept the raw values for calculations but applied number formatting in dashboard-facing cells to display one decimal place.

5. Built a structured table. I converted the data range into an Excel Table (Ctrl + T) so that PivotTables would automatically include any new rows and so that structured references could be used in formulas.

## Exploratory Data Analysis (EDA)

Before finalizing the dashboard structure, I explored the data to understand distributions and identify where the most interesting variation lived.

Questions I started with:

1. What does overall SLA compliance look like, and is 47% actually a problem or expected given priority mix?
2. Are certain agents consistently slower, or is it a workload issue?
3. Does language of support correlate with slower response times or lower SLA compliance?
4. Are any clients disproportionately generating escalations or SLA breaches?


What I found in the initial exploration:

* Overall SLA compliance sat at 47.3%, meaning fewer than half of tickets were resolved within SLA. That is a significant operational signal regardless of how it is benchmarked, and became the framing anchor for the dashboard.

* Agent workloads were not evenly distributed. E. Garcia held 35 tickets and D. Patel held 33, while B. Johnson had only 25. But B. Johnson's average first response time was the highest of all agents at ~71 minutes — similar to A. Smith — which suggested the slower agents were not simply carrying more load.

* SLA compliance varied significantly by language. French-language tickets had the highest compliance rate (~58%), while Spanish had the lowest (~34%). That gap was large enough to warrant its own view.

* EMEA had the best SLA compliance rate (~53%), while APAC had the lowest (~40%). Given that APAC also had slightly higher average response times (~66 mins vs. ~62 mins for EMEA), this pointed to a regional capacity or scheduling issue rather than a ticket complexity issue.

* HealthTech Platform had the lowest SLA compliance of the three clients (~43%), while FinTech SaaS was highest (~52%). Given that HealthTech tickets may carry higher regulatory sensitivity, this was worth surfacing clearly.

## Data Analysis

The analysis was built across four PivotTable-driven views, each answering a specific operational question.

1. Regional & Language Performance

Pivoted ticket counts, average first response time, SLA compliance %, and average resolution time by Region and by Language separately. Key measures:


APAC: Avg response time 66.3 mins, SLA compliance 39.6%
EMEA: Avg response time 61.7 mins, SLA compliance 53.1%
NA: Avg response time 64.7 mins, SLA compliance 49.1%
Spanish: SLA compliance 34.0% — notably the lowest of any language segment


2. Client & SLA Risk Analysis

Pivoted SLA compliance, average response time, and escalation rates by client. Also cross-tabulated priority level against SLA compliance to understand whether high-priority tickets were being handled differently:


Of 51 High-priority tickets, 28 were SLA non-compliant — a 55% breach rate on the most urgent tier
HealthTech Platform: SLA compliance 43.2%, vs. FinTech SaaS at 51.9%
The Unknown client group had a 53.1% SLA compliance rate — in line with average, but worth resolving at source


3. Issue & Process Efficiency

Explored resolution time and escalation patterns by issue type. This view helps identify whether certain issue categories are systematically slower to resolve, which can point to process gaps, training needs, or knowledge base gaps.

4. Agent Performance & Workload

Pivoted tickets per agent alongside average first response time and SLA compliance rate per agent:

AgentTicketsAvg First Response (mins)A. Smith2970.7B. Johnson2570.8C. Moyo2856.1D. Patel3367.0E. Garcia3554.7

C. Moyo and E. Garcia had notably faster response times despite carrying comparable or heavier workloads — a signal worth investigating for coaching purposes.

## Results
#### 1.  Monthly Call Volume Trend: 20.6% Increase from October to November
   <img width="1166" height="341" alt="image" src="https://github.com/user-attachments/assets/280c2dc5-2c9f-4398-b6d4-1a36719ab0a3" />

There is a steady increase in the number of recorded calls from October and November. November witnessed the highest number of calls from 68 calls to 82 calls. Signalling a 20,6% increase.

<img width="1167" height="337" alt="image" src="https://github.com/user-attachments/assets/b9ceff6d-c689-46de-8fd4-450764ad3315" />
*Figure 1: Customer Support Performance & SLA Monitoring Dashboard*


<img width="1162" height="335" alt="image" src="https://github.com/user-attachments/assets/0cf7c8b5-6fbf-4dda-8078-7cc51c462254" />
*Figure 1: Customer Support Performance & SLA Monitoring Dashboard*





On Thursday, October 10, 2025 had the highest recorded calls at 6 total calls for the month of October. 
On Monday, November 10 2025 had the highest recorded calls at a total of 8 calls. Wednesday, the 26th of November 2025 had a total of 6 recorded calls. 
There is a trend that on the 10th of each month there is an increase in the number of calls in spite of which day the 10th falls. This could suggest the need to place more agents on the 10th of every month to cater to the needs of clients who have placed calls.

### 2. Call Distribution by Priority Level (Oct – Nov) 
  <img width="485" height="244" alt="image" src="https://github.com/user-attachments/assets/445b641a-dcc1-4e89-a213-7111084c5cf5" />

Over the two-month period from October to November, support tickets were classified into three priority levels: High, Medium, and Low. Low-priority tickets accounted for the highest volume, with 54 recorded calls, followed closely by High-priority tickets at 51 calls and Medium-priority tickets at 45 calls. The relatively even distribution across priority levels suggests that no single priority category is disproportionately driving ticket volume, indicating that SLA performance is unlikely to be influenced by call priority alone.

### 3. Average First Response Time by Agent (Oct – Nov)

Over the two-month period, only 2 of the 5 active agents recorded an average first response time below the overall average of 64 minutes, while 3 out of 5 agents exceeded the average.

<img width="615" height="234" alt="image" src="https://github.com/user-attachments/assets/aadbfe74-fc2a-4c6c-83da-7fe40914dc1c" />

D. Patel recorded the highest first response time across the period and consistently performed above the average in both October and November, with response times increasing further in November.
The fact that the majority of agents exceed the average first response time, combined with a worsening trend for the highest-performing outlier, suggests a potential skills or workflow efficiency challenge. This is particularly notable given that most calls during the period were classified as low priority, where faster response times would typically be expected.

### 4. Majority of Calls Failed to Meet SLA Targets
Over the two-month period from October to November 2025, 47.3% of calls met SLA targets, while 52.7% did not. In October, SLA compliance stood at 51.47%, but declined to 43.90% in November — a decrease of 7.6 percentage points. Over the same period, SLA non-compliance increased from 48.53% to 56.10%.

<img width="731" height="227" alt="image" src="https://github.com/user-attachments/assets/f99d4890-d42b-4292-84a7-0a346f495d36" />

Notably, November recorded higher call volumes than October, yet SLA performance deteriorated. Instead of scaling positively with increased demand, SLA compliance declined while non-compliance increased. This indicates that current operational capacity and processes are not sufficiently absorbing higher workloads, putting customer satisfaction objectives at risk.
These trends suggest the need for a review of support capacity, skills alignment, and SLA benchmarks to inform a more effective and scalable SLA compliance strategy.

<img width="732" height="222" alt="image" src="https://github.com/user-attachments/assets/ba1901fb-7919-4655-92a5-0f997bfe4f80" /> 


## Recommendations
1. Investigate Spanish-language support coverage first. The 34% SLA compliance rate is too large a gap to be explained by ticket complexity alone. This should be the first thing operations reviews — whether it's a staffing, scheduling, or skills issue.

2. Implement priority-based response routing. High-priority tickets should have a dedicated queue or escalation path that ensures they are picked up faster. The current data suggests high-priority tickets are not consistently getting faster responses.

3. Look at what C. Moyo and E. Garcia are doing differently. These two agents respond ~15 minutes faster on average than A. Smith and B. Johnson while handling similar or higher volumes. Surfacing those patterns — whether it's tool usage, ticket triaging, or communication templates — could help bring up the average.

4. Address APAC scheduling gaps. Slower response times and lower SLA compliance in APAC suggest a coverage window issue. This could be explored by filtering tickets by time-of-day if that field is available in future data pulls.

5. Resolve the "Unknown" client tagging issue. While the Unknown group's SLA performance is not alarming, untagged tickets create blind spots in client reporting and billing. This should be fixed at the source system level.

6. Expand the dataset for trend analysis. One month of data supports a diagnostic snapshot but not trend identification. Adding November and December data would allow the team to identify whether SLA performance is improving, degrading, or holding steady.

## References
SupportYourApp official website: https://supportyourapp.com
GRI Standard 203 (Indirect Economic Impacts) — referenced for client impact framing
Microsoft Excel PivotTable documentation: https://support.microsoft.com
ITIL Service Level Management framework — used as a reference for SLA compliance interpretation
