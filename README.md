# NYC 911 Operational Performance Lakehouse (Databricks DLT)

## Overview

This project implements a **production-grade Lakehouse architecture** for analyzing **NYC 911 emergency response performance** using **Databricks Delta Live Tables (DLT)**.
Its purpose is to transform raw operational 911 data into **trusted, KPI-ready datasets** that power executive dashboards and enable SLA monitoring.

The pipeline follows a **Bronze → Silver → Gold** data model, applies **cloud-native security through AWS IAM and Unity Catalog**, and exposes **business-ready metrics** such as incident volume trends, response time SLAs, and SLA breach rates.

This implementation was intentionally designed to reflect **real enterprise data engineering patterns**, rather than a notebook-only or exploratory workflow.

---

## Architecture

### High-Level Data Flow

```
Raw 911 Data
   ↓
Bronze (Raw Delta Tables)
   ↓
Silver (Cleaned & Normalized)
   ↓
Gold (KPI Aggregations & BI Views)
   ↓
Databricks Dashboards
```

### Key Design Principles

* Clear separation of ingestion, transformation, and analytics
* Delta Lake ACID guarantees enforced at every layer
* IAM-based access with no static credentials
* BI tools consume **only Gold-layer datasets**

---

## Data Pipeline (Bronze → Silver → Gold)

### 🟫 Bronze Layer — Raw Ingestion

* Stores raw NYC 911 operational records as **Delta tables**
* Minimal transformations applied
* Original schema preserved for traceability and replay
* Serves as the immutable system of record

---

### 🪙 Silver Layer — Cleaned & Normalized

* Standardizes timestamps and agency identifiers
* Removes malformed or incomplete records
* Normalizes operational metrics required for KPI calculations
* Designed for reuse across multiple analytical use cases

---

### 🥇 Gold Layer — Analytics & KPIs

* Pre-aggregated, business-ready datasets
* Optimized for BI performance and consistency

**Key metrics include:**

* Total incident volume
* Average call-to-first-pickup time
* Dispatch and pickup response times
* SLA breach percentage

All transformations are orchestrated using **Delta Live Tables (DLT)** to ensure reliability, observability, and maintainability.

---

## Security & IAM Design

This project avoids static credentials entirely and leverages **AWS IAM with Databricks Unity Catalog** for secure, scalable access control.

### IAM Highlights

* Dedicated IAM role (`nyc_911_role`) for Databricks access
* Role assumption via **STS with external ID**
* No access keys stored in code, notebooks, or configuration files

---

### Unity Catalog & External Locations

* Separate external locations defined for:

  * `bronze`
  * `silver`
  * `gold`
  * `catalog`
* Each layer maps to a controlled S3 prefix
* Access centrally governed through Unity Catalog

This setup mirrors **enterprise-grade Databricks security patterns** commonly used in production environments.

---

## Gold KPIs & BI Layer

All business logic is finalized in the **Gold layer**, ensuring dashboards remain:

* Simple
* Fast
* Consistent
* Trustworthy

### Core KPIs

* **Total 911 Incidents (2014–Present)**
* **Monthly Incident Volume by Agency**
* **Average Call-to-First-Pickup Time**
* **SLA Breach Rate (%)**

This approach prevents metric drift and eliminates duplicated logic across BI tools.

---

## Dashboards

Dashboards are built directly on **Gold-layer datasets** and include:

* 📈 Monthly 911 Incident Volume by agency
* ⏱️ Average Call-to-First-Pickup trends
* 🔢 Total Incident Count KPI tile
* 🚨 SLA Breach Rate KPI tile

Dashboards are filterable by agency and optimized for operational monitoring.

> **Important:** No dashboards query Bronze or Silver tables directly.

---

## Engineering Decisions (Why This Matters)

* **Delta Live Tables over notebooks** → improved pipeline reliability and data quality
* **Gold-layer KPIs** → prevents BI logic sprawl
* **IAM + Unity Catalog** → production-grade security
* **Layered S3 storage** → scalable and auditable data layout
* **Separation of SQL and BI logic** → improved maintainability

These decisions align with how modern data platforms are built and operated at scale.

---

## Scalability & Future Enhancements

Potential future extensions include:

* Streaming ingestion (Auto Loader / Kafka)
* SLA breach alerting
* Automated historical backfills
* Agency-level anomaly detection
* Cost optimization using Z-ORDER and clustering

The architecture is intentionally designed to support these enhancements without rework.

---

## Tech Stack

* Databricks
* Delta Live Tables (DLT)
* Delta Lake
* Unity Catalog
* AWS S3
* AWS IAM (STS AssumeRole)
* SQL (Analytics & BI)
* Databricks Dashboards

---

## Project Structure

```
nyc_911_project/
├── architecture/      # Architecture diagrams
├── dashboards/        # Dashboard screenshots
├── dlt/               # Delta Live Tables pipeline definitions
├── iam/               # IAM & Unity Catalog configuration
├── sql/               # SQL transformations and BI views
├── screenshots/       # Supporting visuals
└── README.md
```

---

## Final Notes

This project was built to demonstrate **real-world data engineering practices**, not just technical correctness.
It emphasizes **security, scalability, maintainability, and analytics reliability** — the same principles required in production-grade data platforms.

---

If you want next, I can:

* Tighten this further for **Databricks DE interviews**
* Write a **LinkedIn project post**
* Add a **1–2 sentence recruiter summary** at the top
* Review your **IAM documentation folder**

Just say the word.

