# SQL Analytics & KPI Layer

## Overview

This directory contains all **analytics-grade SQL logic** used to power the NYC 911 Operational Performance dashboards.
All queries are designed to operate **exclusively on Gold-layer datasets**, ensuring metrics are consistent, auditable, and safe for BI consumption.

The SQL layer finalizes business logic for **KPIs, SLA monitoring, and dashboard views**, while keeping transformation complexity out of dashboards and visualization tools.

---

## Design Principles

The SQL layer follows several key principles:

* **Gold-only access** — no queries read from Bronze or Silver tables
* **Single source of truth for KPIs**
* **Window functions over raw aggregation** for dashboard efficiency
* **Explicit SLA logic** defined once and reused everywhere
* **Unity Catalog–managed schemas and views**

This mirrors how analytics SQL is structured in real production data platforms.

---

## Key SQL Components

### KPI Views

<img width="1267" height="628" alt="Sanitycheck_kpi_dashboard" src="https://github.com/user-attachments/assets/826ab8a0-719d-4da6-bdfb-fa38013601d4" />


This view exposes **core operational KPIs** at a weekly, per-agency grain.

**Metrics included:**

* Total incident volume
* Average call-to-first-pickup time
* Average dispatch and arrival times
* Multi-agency incident percentage

**Purpose:**

* Reusable analytics dataset
* Foundation for all downstream KPI calculations
* Optimized for BI joins and filtering

---

### Dashboard KPI View

<img width="944" height="540" alt="Dashboards_nyc_911" src="https://github.com/user-attachments/assets/fb7b1522-91e0-4852-a773-5260390ea6be" />


This view is purpose-built for **executive dashboards** and KPI tiles.

**Logic handled here:**

* SLA breach identification
* Total incident counts (windowed)
* SLA breach counts (windowed)
* SLA breach percentage calculation

By handling SLA logic in SQL, dashboards remain:

* Simple
* Fast
* Free of duplicated business rules

---

## SLA Breach Logic

The SLA breach definition is implemented directly in SQL:

* **SLA Threshold:**
  Average call-to-first-pickup > **5 seconds**

* **Breach Count:**
  Uses conditional aggregation with window functions

* **Breach Percentage:**
  Computed once and reused across all visualizations

This prevents metric drift and ensures every dashboard reflects the **same SLA definition**.

---

## Example KPI Query Pattern
<img width="1267" height="628" alt="Sanitycheck_kpi_dashboard" src="https://github.com/user-attachments/assets/7dda2ec0-3cd9-46d9-bbd2-ae0a40a3d61f" />

```sql
SELECT
  sla_breach_incidents,
  total_incidents_all,
  sla_breach_pct
FROM analytics.vw_911_kpis_dashboard
LIMIT 1;
```

This query directly powers the **SLA Breach % KPI tile** in the dashboard.

---

## Why Window Functions Were Used

Window functions (`SUM() OVER ()`) were intentionally chosen instead of GROUP BY aggregations because they:

* Preserve row-level context
* Support mixed KPI tiles and trend charts
* Avoid separate aggregation queries per metric
* Improve dashboard responsiveness

This approach scales cleanly as new KPIs are added.

---

## Dashboard Integration

The SQL views in this directory are consumed by Databricks Dashboards for:

* 📈 Monthly incident volume trends
* ⏱️ Response time analysis
* 🔢 Total incident KPI tiles
* 🚨 SLA breach rate monitoring

**Important:**
Dashboards never query raw tables — only curated analytics views.

---

## Governance & Access Control

* Views are registered in **Unity Catalog**
* Access is granted at the **schema/view level**
* No direct S3 access is required for BI users
* All data lineage remains visible via Databricks

This ensures strong governance without sacrificing analytics velocity.

---

## Why This Matters (Interview-Ready)

This SQL layer demonstrates:

* Strong separation of transformation vs analytics logic
* Production-grade KPI modeling
* Centralized SLA definitions
* BI-friendly query design
* Enterprise-ready governance patterns

This is exactly how analytics SQL is written in real data engineering teams.

---

## Summary

The SQL layer completes the Lakehouse by transforming Gold-layer data into **trusted, reusable analytics views**.
By centralizing KPI and SLA logic here, the system remains scalable, auditable, and easy to extend as new metrics are introduced.

---



