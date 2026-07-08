# Automotive Sales Pipeline BI Dashboard
A Business Intel solution for automotive sales operations (2025) designed to monitor operational KPIs, identify transaction bottlenecks, and support business decision-making through a star-schema semantic model and interactive Power BI reports.

## Project Background

***This project is based on reporting workflows from a real-world automotive sales operations environment***

In the premium automotive industry, every delayed transaction is a potential lost sale. While dealerships manage hundreds of opportunities simultaneously, identifying where deals stall throughout the sales journey remains a significant operational challenge. Without clear visibility into the sales funnel, managers often rely on intuition rather than data to prioritize follow-ups, coach sales consultants, or allocate resources effectively.

To address this challenge, **Apex Auto Group (APX)** — a fictional premium automotive brand inspired by real-world retail operations — requires a centralized reporting solution that enables both headquarters and dealerships to monitor sales performance in an hourly manner.

This portfolio project simulates a business-oriented Power BI dashboard built for Retail Sales and Sales Operations. The dataset consists of dummy data generated to reflect realistic business scenarios, sales processes, and operational workflows commonly found in premium automotive dealerships.

The dashboard consolidates sales funnel performance into a single operational view, allowing managers to identify bottlenecks, locate delayed transactions, monitor dealer performance, and track progress toward monthly and yearly sales targets. By transforming operational data into actionable insights, the report supports faster decision-making and helps dealerships maximize sales opportunities before they are lost.

Data is refreshed hourly from operational extracts, ensuring managers have timely information to resolve stalled deals and adjust sales strategies when necessary.

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

To support flexible time-based filtering across all funnel stages, these tables were consolidated into a single fact table — allowing slicers and date dimensions to operate consistently across the entire report without cross-table complexity.

Deduplication is handled at the consolidation step. Since each source table contains an `updated_date_time` field tracking the latest record state, a `ROW_NUMBER()` window function partitioned by transaction key is applied to retain only the most recent record per transaction, ensuring one true lineage path is preserved throughout the funnel.

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
Full Payment Delayed consistently recorded the highest delay volume across all reporting periods, reaching 17,494 delayed transactions YTD, approximately twice the volume of either Delivery Request or SCC delays. This indicates that payment collection, rather than downstream logistics, is the primary operational bottleneck affecting transaction completion.

### Order Finalization is the Biggest Bottleneck in the Active Pipeline
Order Finalization Pending consistently held the highest count among all four pending categories — 2,909 in July, 2,813 in December, and 1,486 YTD (the lower YTD figure reflects resolved cases). The sub-breakdown reveals Full Payment Pending and Delivery Request Pending as the two primary contributors, directly linked to Finding 2. Unblocking Order Finalization is the single highest-leverage action available to improve monthly SCC output.

### Sales Volume Is Highly Concentrated in Two Flagship Showrooms
Apex Manchester and Apex London together account for the majority of transaction volume across all stages. In the YTD view, Apex Manchester alone generated approximately 57,000 leads (~35% of total network volume). While this concentration reflects strong market presence in key locations, it also creates dependency risk — any operational disruption at either showroom disproportionately impacts network-level KPIs.

### Sales Performance Depends Heavily on Financing Efficiency, Indicating Financing Health is Critical to Sales Velocity
Bank Financing accounted for 44.69% of overall payment methods and 44.61% of COI payment methods on a YTD basis — consistent across both MTD periods as well. Given that Full Payment Delay is the leading operational bottleneck, the heavy reliance on bank financing means that the quality of financing partnerships and approval turnaround times directly influence the dealership's ability to close transactions on schedule.

### Aethel and Centaur Drive the Majority of Brand Volume
On a YTD basis, Aethel (36,394 units) and Centaur (31,932 units) together represent approximately 39% of total lead volume across the network. Passenger Car dominates segment mix at ~82% of total, with C-Class accounting for the largest share within model class. These concentrations have direct implications for inventory planning and marketing resource allocation.

