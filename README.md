# NYC 911 Operational Performance Lakehouse (Databricks DLT)

## Overview

This project implements a **production-grade Lakehouse architecture** for analyzing **NYC 911 emergency response performance** using **Databricks Delta Live Tables (DLT)**.

The goal is to transform raw NYC 911 operational data into **trusted, KPI-ready datasets** that power **executive dashboards, SLA monitoring, and operational analytics**, while enforcing **enterprise-grade security and governance**.

The solution follows a **Bronze → Silver → Gold** data model, leverages **AWS IAM and Databricks Unity Catalog** for access control, and ensures **all business logic is finalized in the Gold layer** to prevent metric drift.

This implementation reflects **real-world data engineering patterns**, not a notebook-only or exploratory workflow.

---

## Data Source

**NYC Open Data — 911 End-to-End Operational Dataset**

* Publisher: NYC Open Data
* Dataset: *911 End-to-End Data*
* Domain: Public Safety
* Format: CSV
* Time Range: 2014 – Present
* Granularity: Weekly, agency-level metrics

🔗 **Official Source:**
[https://data.cityofnewyork.us/Public-Safety/911-End-to-End-Data/t7p9-n9dy/about_data](https://data.cityofnewyork.us/Public-Safety/911-End-to-End-Data/t7p9-n9dy/about_data)

The dataset includes:

* Incident volume counts
* Call-to-first-pickup response times
* Dispatch and arrival metrics
* Multi-agency response indicators
* SLA-relevant timing fields

Raw data is ingested **as-is** into the Bronze layer to preserve lineage, auditability, and replayability.

---

## Production Architecture

### End-to-End Lakehouse Flow

```
NYC Open Data (CSV)
        ↓
AWS S3 (Raw / Bronze)
        ↓
Delta Live Tables (DLT)
        ↓
Bronze (Immutable Raw Delta Tables)
        ↓
Silver (Validated & Analytics-Ready)
        ↓
Gold (Curated KPIs & Aggregations)
        ↓
Databricks Dashboards (Gold-only)
```

### Architecture Diagram

<img width="1536" height="1024" alt="nyc911 lakehouse architecture" src="https://github.com/user-attachments/assets/ed76ebb5-9955-4fb8-89f4-5a87b5530e47" />


> This architecture enforces **Gold-only consumption**, strict IAM security boundaries, and centralized governance via Unity Catalog.

---

## Architecture Design Principles

* Clear separation of ingestion, transformation, and analytics
* Delta Lake ACID guarantees at every layer
* IAM-based access (no static credentials)
* Gold-only BI consumption
* Centralized governance via Unity Catalog
* SQL-first, auditable transformations
* Scalable S3-backed storage layout

---

## Data Pipeline (Bronze → Silver → Gold)

### 🟫 Bronze Layer — Immutable System of Record

**Purpose**

* Preserve raw NYC 911 operational data exactly as received

**Characteristics**

* Raw CSV ingested via Delta Live Tables
* Minimal schema casting for basic queryability
* No business logic applied
* Supports lineage, auditing, and replay

**Storage**

* S3 external location: `/bronze/`

---

### 🪙 Silver Layer — Validated & Normalized Data

**Purpose**

* Prepare high-quality datasets for analytics and KPIs

**Transformations**

* Timestamp normalization
* Agency and incident type standardization
* Removal of malformed or incomplete records
* Field-level validation and deduplication
* SLA-relevant bounds enforcement

**Design**

* Reusable analytical foundation
* Clean separation from raw ingestion concerns

---

### 🥇 Gold Layer — Business KPIs & Aggregations

**Purpose**

* Deliver business-ready datasets for dashboards and reporting

**KPIs Produced**

* Total 911 incident volume
* Average call-to-first-pickup time
* Dispatch and arrival response times
* Multi-agency incident percentage
* SLA breach count
* SLA breach rate (%)

**Design Decisions**

* KPI logic finalized at the data layer
* Pre-aggregated metrics for performance
* Prevents BI-layer logic duplication

---

## Delta Live Tables (DLT)

All transformations are orchestrated using **Delta Live Tables**, providing:

* Declarative pipeline definitions
* Automatic dependency resolution
* Incremental processing
* Built-in observability and monitoring
* Production-grade reliability

DLT replaces ad-hoc notebooks with a **maintainable, auditable pipeline**.

---

## Security & Governance

This project intentionally avoids static credentials and applies **enterprise-grade cloud security**.

### AWS IAM

* Dedicated IAM role: `nyc_911_role`
* Role assumption via **STS + External ID**
* No access keys stored in code or notebooks
* Least-privilege access to S3

### Unity Catalog

* Centralized governance for schemas, tables, and views
* Controlled access via IAM-backed external locations
* Enforces Gold-only consumption for BI

### External Storage Layout

| Layer   | S3 Prefix                              |
| ------- | -------------------------------------- |
| Bronze  | `s3://nyc-911-lakehouse-usw2/bronze/`  |
| Silver  | `s3://nyc-911-lakehouse-usw2/silver/`  |
| Gold    | `s3://nyc-911-lakehouse-usw2/gold/`    |
| Catalog | `s3://nyc-911-lakehouse-usw2/catalog/` |

---

## SQL & Analytics Layer

SQL is used for:

* KPI aggregation logic
* SLA breach calculations
* Analytical views for dashboards
* Sanity checks and validation queries

All SQL logic is **version-controlled, auditable, and reusable**.

Dashboards consume **Gold-layer views only** — never Bronze or Silver tables.

---

## Dashboards

Dashboards are built directly on Gold datasets and include:

* 📈 Monthly 911 Incident Volume by Agency
* ⏱️ Average Call-to-First-Pickup Trends
* 🔢 Total Incident Count KPI
* 🚨 SLA Breach Rate (%) KPI

Designed for **operational monitoring and executive visibility**.

---

## Project Structure

```
nyc_911_project/
├── architecture/      # Architecture diagrams & documentation
├── dashboards/        # Dashboard definitions
├── dlt/               # Delta Live Tables pipeline definitions
├── iam/               # IAM & Unity Catalog configuration
├── sql/               # SQL transformations & KPI views
└── README.md          # Project overview
```

---

## Why This Architecture Matters

* **DLT instead of notebooks** → reliability and observability
* **Gold-layer KPIs** → single source of truth
* **IAM + Unity Catalog** → production-grade security
* **Layered S3 design** → scalable and auditable
* **SQL-first logic** → transparency and maintainability

This mirrors how **modern enterprise data platforms** are built in production.

---

## Scalability & Future Enhancements

The architecture is intentionally designed to scale without rework:

* Streaming ingestion (Auto Loader / Kafka)
* SLA breach alerting
* Automated historical backfills
* Agency-level anomaly detection
* Cost optimization via Z-ORDER and clustering

---

## Final Notes

This project demonstrates **real-world data engineering practices**, not just technical correctness.

It emphasizes:

* Security
* Governance
* Scalability
* Maintainability
* Analytics reliability

The same principles required in **production public-sector and enterprise data platforms**.

---



