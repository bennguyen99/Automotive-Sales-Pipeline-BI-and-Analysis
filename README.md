# Automotive Sales Pipeline BI Dashboard
An Automotive Sales Operations BI solution (2025) designed to monitor operational KPIs, identify transaction bottlenecks, and support business decision-making through a star-schema semantic model and interactive Power BI reports.

## Project Background

***This project is based on reporting workflows from a real-world automotive sales operations environment***

In the premium automotive industry, every delayed transaction is a potential lost sale. While dealerships manage hundreds of opportunities simultaneously, identifying where deals stall throughout the sales journey remains a significant operational challenge. Without clear visibility into the sales funnel, managers often rely on intuition rather than data to prioritize follow-ups, coach sales consultants, or allocate resources effectively.

To address this challenge, **Apex Auto Group (APX)** — a fictional premium automotive brand inspired by real-world retail operations — requires a centralized reporting solution that enables both headquarters and dealerships to monitor sales performance in real time.

This portfolio project simulates an enterprise-style Power BI dashboard built for Retail Sales and Sales Operations. The dataset consists of dummy data generated to reflect realistic business scenarios, sales processes, and operational workflows commonly found in premium automotive dealerships.

The dashboard consolidates sales funnel performance into a single operational view, allowing managers to identify bottlenecks, locate delayed transactions, monitor dealer performance, and track progress toward monthly and yearly sales targets. By transforming operational data into actionable insights, the report supports faster decision-making and helps dealerships maximize sales opportunities before they are lost.

Data is refreshed hourly from operational extracts, ensuring managers have timely information to resolve stalled deals and adjust sales strategies when necessary.

## Data Structure

This project adopts a Medallion Architecture with two layers: **Silver and Gold**, **omitting the Bronze layer** since raw source data was already extracted and delivered in a structured format.

In the **Silver layer**, raw retail data is restructured, cleansed, and enriched to ensure consistency and completeness before downstream use.

In the **Gold layer**, data is transformed into a business-ready star schema optimised for reporting. The retail domain was originally split across multiple source tables, each representing a different stage of the sales process. 

<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-Dashboard/main/Images/ETL%20Process.drawio.png" alt="Data Flow"/>
</p>

To support flexible time-based filtering across all funnel stages, these tables were consolidated into a single fact table — allowing slicers and date dimensions to operate consistently across the entire report without cross-table complexity.

Deduplication is handled at the consolidation step. Since each source table contains an `updated_date_time` field tracking the latest record state, a `ROW_NUMBER()` window function partitioned by transaction key is applied to retain only the most recent record per transaction, ensuring one true lineage path is preserved throughout the funnel.

An oversimplified version of the report is depicted below:

<p align="center">
  <img src="https://raw.githubusercontent.com/bennguyen99/Automotive-Sales-Pipeline-BI-Dashboard/main/Images/Relationship%20Diagram.drawio.png" alt="Data Model - Relationship Diagram"/>
</p>

With a single fact table as the reporting foundation, Row-Level Security (RLS) implementation is straightforward: all access control is enforced through a clean fact-to-dimension relationship (`factretail_local` → `dimrls_local`), avoiding the complexity and risks associated with many-to-many relationships.

## Executive Summary



