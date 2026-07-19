# Automotive Sales Pipeline BI and Analysis
<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-and-Analysis/main/Images/Apex%20Banner.png" alt="APX Banner"/>
</p>
A Business Intel solution for automotive sales operations (2025) designed to monitor operational KPIs, identify transaction bottlenecks, and support business decision-making through a star-schema semantic model and interactive Power BI reports. This BI report is inspired by a real-world enterprise reporting solution that I worked on.

## Project Background

In the premium automotive industry, every delayed transaction is a potential lost sale. While dealerships manage hundreds of active opportunities simultaneously, identifying where deals stall across the sales journey remains a significant operational challenge. Without real-time pipeline visibility, managers often rely on intuition rather than data to prioritize follow-ups, coach consultants, and allocate resources, resulting in missed targets and preventable revenue loss.

To address this, Apex Auto Group England (APX-ENG), a regional branch of a fictional premium automotive brand, requires a centralized reporting solution that gives both headquarters and dealership managers timely visibility into sales performance and pipeline health.

This project delivers a business-oriented Power BI dashboard built for Retail Sales and Sales Operations, powered by a simulated dataset designed to reflect realistic automotive sales processes, transaction workflows, and operational scenarios.

The dashboard consolidates the full sales funnel (from Lead generation to Sales Card Capture (SCC)) into a single operational view, enabling managers to identify bottlenecks, locate delayed transactions, monitor dealer and consultant performance, and track progress against monthly and yearly targets. 

Data is refreshed hourly, ensuring decisions are based on current pipeline state rather than yesterday's numbers.

## Data Structure

This project adopts a Medallion Architecture with two layers: **Silver and Gold**, **omitting the Bronze layer** since raw source data was already extracted and delivered in a structured format.

In the **Silver layer**, raw retail data is restructured, cleansed, and enriched to ensure consistency and completeness before downstream use.

In the **Gold layer**, data is transformed into a business-ready star schema optimised for reporting. The retail domain was originally split across multiple source tables, each representing a different stage of the sales process. 

<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-Dashboard/main/Images/ETL%20Process.drawio.png" alt="Data Flow"/>
</p>
<p align="center"> 
  <i> <b> Data Architecture </b>: The architecture follows the medallion architecture </i> 
</p>

To support flexible time-based filtering across all funnel stages, these tables were consolidated into a single fact table, allowing slicers and date dimensions to operate consistently across the entire report without cross-table complexity.

**Deduplication** is handled at the consolidation step. Since **each source table** contains an `updated_date_time` field that tracks the latest record state, a `ROW_NUMBER()` window function, partitioned by transaction key, is applied to retain only the most recent record per transaction, ensuring a single true lineage path is preserved throughout the funnel.

```
-- deduplicate by finding the latest record of a sales transaction
ROW_NUMBER() OVER (
PARTITION BY transaction_id
ORDER BY updated_date_time DESC
)
```

The simplified reporting data model is illustrated below.

<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-Dashboard/main/Images/Relationship%20Diagram.png" alt="Data Model - Relationship Diagram"/>
</p>
<p align="center"> 
  <i> <b> Data Model </b>: The Star Schema above is a simplified version of the real work </i> 
</p>

With a single fact table as the reporting foundation, Row-Level Security (RLS) implementation is straightforward: all access control is enforced through a clean fact-to-dimension relationship (`factretail_local` → `dimrls_local`), avoiding the complexity and risks associated with many-to-many relationships.

## Dashboard Overview

<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-Dashboard/main/Images/Apex%20Retail%20Operation%20Report%20MTD.png" alt="MTD View"/>
</p>
<p align="center"> 
  <i> <b> Overview </b>: MTD View </i> 
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-Dashboard/main/Images/Apex%20Retail%20Operation%20Report%20YTD.png" alt="YTD View"/>
</p>
<p align="center"> 
  <i> <b> Overview </b>: YTD View </i> 
</p>

## Executive Summary

Despite a strong improvement in sales execution during the second half of 2025, Apex Auto Group finished the year with only 42.3% target achievement and an overall Lead-to-SCC conversion rate of 16.6%, indicating substantial room to improve pipeline efficiency. The analysis shows that operational delays—particularly Full Payment and Order Finalization—remain the primary constraints preventing higher transaction completion rather than insufficient customer demand.

The report also highlights a high concentration of business performance across Apex Manchester and Apex London, together with a heavy reliance on bank financing, which accounts for nearly 45% of all completed transactions. These findings suggest that improving payment processing efficiency, reducing operational bottlenecks, and mitigating dependency on key dealerships would likely deliver the greatest impact on future sales performance.

## Key Findings

### Sales Target Achievement Improved Significantly Across the Year
MTD Target Achievement rose from 23.29% in July to 42.29% in December, indicating a strong performance recovery in H2 2025. On a YTD basis, the network closed the year at 42.29% target achievement with 27,398 SCC transactions recorded against a total lead volume of 164,670 — representing an overall funnel conversion rate of approximately 16.6% from Lead to SCC.

### Full Payment Delay is the Most Persistent Operational Risk
Full Payment Delayed consistently recorded the highest delay volume across all reporting periods, reaching 17,494 delayed transactions YTD, approximately twice the volume of either Delivery Request or SCC delays and accounting for over 30% of all Order Finalization transactions. This indicates that payment collection, rather than downstream logistics, is the primary operational bottleneck constraining transaction completion across the network.

### Order Finalization is the Biggest Bottleneck in the Active Pipeline
Order Finalization Pending consistently held the highest count among all four pending categories — 2,909 in July, 2,813 in December, and 1,486 YTD (the lower YTD figure reflects resolved cases). The sub-breakdown reveals Full Payment Pending and Delivery Request Pending as the two primary contributors. Unblocking Order Finalization is the single highest-leverage action available to improve monthly SCC output.

To support this, the report's transaction-level drill-down allows Sales Managers to move beyond aggregate metrics and act on individual stalled deals directly from the dashboard.

<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-Dashboard/main/Images/Raw%20Data%20Table.png" alt="Raw Table filtered"/>
</p>
<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-Dashboard/main/Images/Delayed%20by%20Different%20Stakeholder%20Types.png" alt="Status and Delayed Status filter"/>
</p>
<p align="center"> 
  <i> <b> Drill-down feature </b>: Allow BUs to locate all information relating to the delayed transactions and take actions immediately </i> 
</p>

### Sales Volume Is Highly Concentrated in Two Flagship Showrooms
Apex Manchester and Apex London together account for the majority of transaction volume across all stages. In the YTD view, Apex Manchester alone generated approximately 57,000 leads (~35% of total network volume). While this concentration reflects strong market presence in key locations, it also creates dependency risk, as any operational disruption at either showroom would disproportionately impact network-level KPI performance.

### Financing Efficiency is a Direct Lever on Sales Velocity
Bank Financing accounted for 44.69% of overall payment methods and 44.61% of COI payment methods on a YTD basis — consistent across both MTD periods as well. Given that Full Payment Delay is the leading operational bottleneck, the heavy reliance on bank financing means that the quality of financing partnerships and approval turnaround times directly influence the dealership's ability to close transactions on schedule.

### Aethel and Centaur Drive the Majority of Brand Volume
On a YTD basis, Aethel (36,394 units) and Centaur (31,932 units) together represent approximately 39% of total lead volume across the network. Passenger Car dominates segment mix at ~82% of total, with C-Class accounting for the largest share within model class. These demand concentrations have direct implications for inventory planning, marketing resource allocation, and import prioritization heading into 2026.

## Recommendations
### 1. Prioritize Full Payment Processing to Reduce Pipeline Delays

Full Payment Delayed recorded 17,494 delayed transactions YTD—nearly twice the volume of Delivery Request and SCC delays. With 44.7% of transactions financed by banks, financing approval speed is a key driver of sales throughput.

#### Recommended actions

  *  Establish a 3-business-day SLA with financing partners for loan approvals.
  *   Trigger automated alerts for transactions pending payment 5+ business days after Order Finalization.
  *    Assign dedicated finance liaisons at Apex Manchester and Apex London to expedite financing cases.

#### Expected impact: Shorter sales cycles, fewer payment delays, and higher Lead-to-SCC conversion.

### 2. Improve Order Finalization Through Better Workflow Management

Order Finalization Pending remained the largest active bottleneck throughout the year, largely driven by Full Payment Pending and Delivery Request Pending.

#### Recommended actions

  * Monitor transactions pending 7+ days through an ageing dashboard.
  * Separate ownership between Finance (payment follow-up) and Operations (delivery scheduling).
  * Review cancelled Order Finalization cases monthly to identify recurring issues by showroom, brand, or consultant.

#### Expected impact: Faster pipeline progression, improved SCC achievement, and earlier identification of at-risk deals.

### 3. Reduce Operational Dependency on High-Volume Dealerships

Apex Manchester generated approximately 35% of total network leads, creating concentration risk if performance declines at key locations.

#### Recommended actions

  * Standardize best-performing sales practices from Manchester and London across other dealerships.
  * Set quarterly growth targets for lower-volume dealerships with dedicated Sales Operations support.
  * Track a Top2 Showroom Share KPI to monitor network concentration over time.

#### Expected impact: More balanced dealership performance and improved operational resilience.

### 4. Strengthen Financing Partnerships

With nearly 45% of completed transactions financed through banks, financing efficiency directly influences transaction completion.

#### Recommended actions

  * Review financing partners quarterly using approval rate and turnaround time.
  * Expand Leasing as an alternative financing option for eligible customer segments.
  * Introduce financing pre-qualification during the quotation stage to identify risks earlier.

#### Expected impact: Faster financing approvals and smoother progression from Order Finalization to Handover.

### 5. Align Inventory and Marketing with Customer Demand

Aethel and Centaur contributed approximately 39% of total leads, while Passenger Cars accounted for 82% of demand.

#### Recommended actions

  * Prioritize inventory planning for high-demand brands and vehicle classes.
  * Reallocate marketing investment from low-performing brands to stronger product lines.
  * Track Lead-to-SCC conversion by brand to distinguish high-interest brands from high-performing brands.

#### Expected impact: Higher inventory turnover, improved campaign ROI, and better alignment between supply and market demand.

## Dashboard Features

### Sales Funnel with MTD/YTD Toggle
- Track the complete sales pipeline from **Lead** to **SCC** alongside **Target Achievement**.
- Instantly switch between **MTD** and **YTD** views using bookmark navigation.
- *Order Bank* is always displayed on a **YTD** basis as it represents active committed orders.

### Pending & Delay KPI Monitoring
- Monitor **Pending** and **Delayed** transactions across critical sales stages.
- Quickly identify operational bottlenecks requiring immediate follow-up.

### Transaction-Level Investigation
- Drill from summary KPIs to individual transactions using **Stage** and **Delay Status** filters.
- Identify stalled deals, assigned consultants, showrooms, and key dates for immediate action.

### Performance Breakdown
- Analyze performance by **Sales Consultant, Dealer, Showroom, Brand, Segment, Model Class,** and **Body Type**.
- Independent **Stage** and **Status** slicers enable focused analysis without affecting overall KPIs.

### Payment Method Analysis
- Compare transaction volume and share across payment methods, including **COI** transactions.
- Monitor financing mix and customer payment behavior.

### Multi-Dimensional Filtering
- Slice data by **Dealer, Showroom, Brand, Segment, Model, Sales Channel, Transaction Type, Fleet, EMH,** and **Concession**.
- Support both executive-level monitoring and transaction-level operational analysis.

### Role-Based Access Control
- Restrict report access by **Dealer** and **Showroom** using **Row-Level Security (RLS)**.
- Ensure users only view data relevant to their assigned business unit while maintaining a single semantic model.

