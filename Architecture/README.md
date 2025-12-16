# 📐 Architecture — NYC 911 Operational Performance Lakehouse

## Overview

This project implements a **production-grade Lakehouse architecture** for analyzing NYC 911 emergency response performance using **Databricks Delta Live Tables (DLT)**.

The architecture is designed to reliably transform **raw operational 911 data** into **trusted, KPI-ready datasets** that support:

* Executive dashboards
* SLA monitoring
* Operational performance analysis

Rather than a notebook-driven or exploratory workflow, this system mirrors **real enterprise data platform patterns**, emphasizing:

* Clear data layer responsibilities
* Strong security and governance
* Scalable, observable pipelines
* Clean separation between data engineering and BI

---

## High-Level Architecture Flow

```
NYC 911 Raw Data (CSV)
        ↓
Bronze Layer (Delta Live Tables)
        ↓
Silver Layer (Validated & Normalized)
        ↓
Gold Layer (Business KPIs)
        ↓
Unity Catalog Views
        ↓
Databricks Dashboards
```

Each layer serves a **single, well-defined responsibility**, ensuring reliability, auditability, and maintainability as the platform scales.

---

## Architecture Diagram

<img width="1536" height="1024" alt="nyc911 lakehouse architecture" src="https://github.com/user-attachments/assets/7c56a02d-daec-43db-9bd9-37ebf48ab2b8" />


The diagram visually represents:

* End-to-end data flow
* Layer isolation
* Security boundaries
* BI consumption path

> **Important:** Dashboards and BI tools **only** consume Gold-layer datasets — never Bronze or Silver.

---

## Data Layer Responsibilities

### 🟫 Bronze Layer — Raw Ingestion

**Purpose:** Preserve raw operational data with minimal transformation.

**Key Characteristics:**

* Ingests raw NYC 911 CSV files
* Schema is sanitized but not heavily transformed
* Columns are cast to appropriate data types
* No business logic applied
* Serves as the **immutable system of record**

**Why this matters:**

* Enables data replay and backfills
* Supports audit and lineage tracking
* Prevents early data loss or distortion

---

### 🪙 Silver Layer — Cleaned & Normalized

**Purpose:** Produce analytics-ready, trustworthy data.

**Key Transformations:**

* Standardized timestamps
* Validated agency identifiers
* Filtered malformed or incomplete records
* Enforced reasonable value ranges (e.g., response times)

**Design Intent:**

* Optimize for reuse across multiple downstream use cases
* Centralize data quality enforcement
* Prevent duplicated cleaning logic in BI or ML layers

---

### 🥇 Gold Layer — Business KPIs

**Purpose:** Deliver **final, authoritative metrics** for reporting and decision-making.

**Gold Layer Outputs:**

* Weekly incident volume by agency
* Average call-to-first-pickup time
* Dispatch and arrival response times
* Multi-agency incident percentage
* SLA breach counts and breach rate (%)

**Why all KPIs live here:**

* Eliminates metric drift
* Ensures dashboards are fast and consistent
* Keeps BI logic simple and declarative

All Gold transformations are implemented using **DLT-managed SQL**, ensuring reliability, observability, and incremental processing.

---

## Orchestration & Processing Model

### Delta Live Tables (DLT)

The pipeline is orchestrated entirely with **Databricks Delta Live Tables**, providing:

* Declarative pipeline definitions
* Automatic dependency resolution
* Built-in observability and lineage
* Incremental processing support
* Production-safe execution semantics

This approach replaces brittle, manually sequenced notebooks with a **managed, auditable pipeline**.

---

## Security & Governance Architecture

Security is a **first-class design concern**, not an afterthought.

### Key Principles

* No static credentials
* Role-based access control
* Centralized governance
* Layer-level isolation

### Implementation Highlights

* AWS IAM role assumption via STS
* External locations mapped per data layer (Bronze/Silver/Gold)
* Unity Catalog enforces access policies
* BI users access curated views only

📌 **Detailed security documentation:**
See [`/iam/README.md`](../iam/README.md)

---

## BI & Analytics Consumption

Dashboards are built directly on **Gold-layer views** registered in Unity Catalog.

### Benefits of This Design

* Dashboards remain lightweight
* No embedded business logic in BI
* Faster query performance
* Single source of truth for KPIs

This prevents common enterprise anti-patterns such as:

* KPI logic duplicated across dashboards
* Inconsistent SLA definitions
* Performance issues from querying raw data

---

## Engineering Decisions & Tradeoffs

### Why Bronze / Silver / Gold?

* Clear separation of concerns
* Easier debugging and data quality enforcement
* Scales naturally as data volume and use cases grow

### Why DLT Instead of Notebooks?

* Deterministic execution
* Built-in observability
* Safer incremental processing
* Easier long-term maintenance

### Why Gold-Only BI Access?

* Prevents accidental misuse of raw data
* Enforces consistent metric definitions
* Aligns with enterprise analytics best practices

---

## Scalability & Future Enhancements

The architecture is intentionally extensible.

Potential future improvements include:

* Streaming ingestion (Auto Loader or Kafka)
* SLA breach alerting
* Historical backfill automation
* Cost optimization via Z-ORDER and clustering
* Anomaly detection on response times
* ML-driven incident forecasting

None of these require architectural rework — only incremental additions.

---

## Final Notes

This architecture reflects **real-world data platform design**, not a toy example.

It prioritizes:

* Reliability over shortcuts
* Governance over convenience
* Long-term maintainability over quick wins

The result is a Lakehouse that can realistically support operational analytics at scale.

---



