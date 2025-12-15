
---

# Delta Live Tables (DLT) Pipeline — NYC 911 Lakehouse

## Overview

This directory contains the **Delta Live Tables (DLT)** pipeline that powers the NYC 911 Operational Performance Lakehouse.

The pipeline is implemented using **SQL-based Delta Live Tables** and follows a **Bronze → Silver → Gold** architecture to transform raw NYC 911 operational data into **trusted, KPI-ready datasets** used by downstream analytics and dashboards.

DLT is used intentionally to provide:

* Declarative data transformations
* Built-in data quality enforcement
* Pipeline observability
* Incremental processing at scale

This mirrors how production lakehouse pipelines are implemented in enterprise Databricks environments.

---

## Pipeline Architecture

**High-level flow:**

```
Raw NYC 911 CSV (S3)
        ↓
Bronze Layer (Raw, Typed Delta)
        ↓
Silver Layer (Validated & Normalized)
        ↓
Gold Layer (Aggregated KPIs)
        ↓
Databricks Dashboards
```

The pipeline graph clearly reflects this flow, with each layer materialized as a managed DLT table.

---

## Bronze Layer — Raw Ingestion

<img width="1265" height="628" alt="dlt_bronze_911 sql" src="https://github.com/user-attachments/assets/214ce017-60b5-427b-982a-68554b23c075" />


### Purpose

The Bronze layer ingests raw NYC 911 CSV data from S3 and applies **minimal transformation** to establish a typed, queryable Delta table.

### Key Characteristics

* Reads raw CSV files from the Bronze S3 prefix
* Casts columns into appropriate data types
* Preserves source structure for traceability
* Acts as the **immutable system of record**

### Design Rationale

* No business logic is applied at this stage
* Enables replay and reprocessing if upstream data changes
* Keeps ingestion simple and reliable

---

## Silver Layer — Cleaned & Normalized

<img width="1273" height="633" alt="dlt_sliver_911 sql" src="https://github.com/user-attachments/assets/065bb22e-5603-414a-aa9e-bbc51733f1ff" />


### Purpose

The Silver layer applies **data validation and normalization**, preparing the dataset for analytics and KPI computation.

### Transformations Applied

* Filters out malformed or incomplete records
* Enforces required fields (dates, agencies, incident types)
* Removes out-of-range values (e.g., unrealistic response times)
* Normalizes time-based operational metrics

### Data Quality Controls

* Null checks on critical fields
* Value bounds on response-time columns
* Consistent agency and incident-type representation

### Design Rationale

* Ensures analytics are computed only on **trusted data**
* Centralizes cleaning logic to avoid duplication
* Supports reuse across multiple downstream use cases

---

## Gold Layer — KPI Aggregations

<img width="1270" height="631" alt="dlt_gold__kpis sql" src="https://github.com/user-attachments/assets/ce563947-2f22-4690-a633-587230c6e647" />


### Purpose

The Gold layer produces **business-ready KPIs** optimized for dashboards and executive reporting.

### Metrics Computed

* Total incident volume
* Average call-to-first-pickup time
* Dispatch and arrival response times
* Agency-level operational performance
* Multi-agency incident percentage
* SLA-related performance indicators

### Optimization

* Pre-aggregated metrics for fast dashboard queries
* Weekly grain aligned with operational reporting needs
* No downstream BI logic required

### Design Rationale

* Prevents metric drift across dashboards
* Ensures consistent KPI definitions
* Keeps BI layers simple and performant

---

## DLT Features Used

This pipeline leverages core Delta Live Tables capabilities:

* **Declarative SQL transformations**
* **Materialized views** for each layer
* **Incremental processing**
* **Built-in dependency tracking**
* **Pipeline graph observability**

DLT manages execution order, retries, and lineage automatically.

---
<img width="1268" height="632" alt="DLT_Pipeline_Graph" src="https://github.com/user-attachments/assets/14c087d6-3a04-4b76-ac2c-446850eaead5" />


## Operational Visibility

The pipeline provides clear operational insight via:

* Visual pipeline graph
* Record counts at each stage
* Incremental vs full refresh visibility
* Execution duration tracking

This makes it suitable for monitoring in production environments.

---

## Why Delta Live Tables?

DLT was chosen instead of ad-hoc notebooks to ensure:

* Repeatable, reliable pipelines
* Clear separation of concerns
* Easier debugging and maintenance
* Production-grade observability
* Cleaner handoff to analytics teams

This reflects how modern lakehouse pipelines are deployed at scale.

---

## File Structure

```
dlt/
├── bronze_911.sql        # Raw ingestion
├── silver_911.sql        # Validation & normalization
├── gold_911_kpis.sql     # KPI aggregations
├── DLT_Pipeline_graph    # DLT Pipeline
└── README.md             # This documentation
```

---

## Key Takeaways

* Bronze preserves raw data integrity
* Silver enforces trust and quality
* Gold delivers analytics-ready KPIs
* DLT orchestrates everything declaratively
* BI consumes **only Gold-layer outputs**

This pipeline is designed for **real-world operational analytics**, not experimentation.

---



