# SQL Layer — Analytics & Validation (NYC 911 Lakehouse)

## Overview

This directory contains **SQL queries and views** used to support analytics validation, KPI verification, and BI consumption for the NYC 911 Operational Performance Lakehouse.

SQL is intentionally used **only where it adds clarity and control**, such as:

* Verifying Gold-layer outputs
* Creating KPI-ready views
* Performing sanity checks
* Supporting dashboards and stakeholder analysis

All core transformations are handled upstream by **Delta Live Tables (DLT)**.
This SQL layer consumes **only trusted Silver and Gold datasets**.

---

## Role of SQL in This Architecture

SQL is used **after** data engineering logic has been finalized, not during ingestion.

### Responsibilities of the SQL Layer

* Validate DLT outputs
* Expose analytics-ready views
* Support dashboard queries
* Perform targeted analysis without duplicating pipeline logic

### Responsibilities Explicitly Avoided

* No raw ingestion logic
* No data cleansing logic
* No business logic duplication from DLT
* No queries against Bronze tables

This separation keeps the platform **clean, maintainable, and auditable**.

---

## Data Access Pattern

```
DLT Gold Tables
      ↓
SQL Views / Validation Queries
      ↓
Databricks Dashboards
```

All SQL queries assume:

* Unity Catalog governance
* IAM-based access
* Gold-layer schema stability

---

## SQL Use Cases

### 1. KPI Validation

SQL is used to:

* Verify aggregation logic
* Spot-check averages and counts
* Confirm SLA metrics before dashboards consume them

Example validations include:

* Incident counts by agency
* Average response times
* Multi-agency incident percentages

---

### 2. KPI-Ready Views

Some SQL files expose **read-only views** to:

* Simplify BI consumption
* Standardize metric naming
* Reduce dashboard query complexity

This ensures dashboards remain:

* Fast
* Simple
* Consistent
* Free of embedded logic

---

### 3. Sanity Checks

SQL sanity checks help answer questions like:

* Are response times within realistic bounds?
* Do weekly aggregates align with expectations?
* Are nulls or outliers appearing unexpectedly?

These checks act as a lightweight quality gate on top of DLT.

---

## Design Decisions

### Why Not Do Everything in SQL?

* DLT provides lineage, retries, and observability
* SQL alone lacks pipeline-level guarantees
* Business logic belongs upstream, not in BI queries

### Why Still Use SQL?

* SQL excels at validation and analytics
* Analysts and stakeholders understand SQL
* Dashboards benefit from simple, stable queries

This balance reflects real-world enterprise data platforms.

---

## Governance & Security

All SQL execution is governed by:

* Unity Catalog permissions
* IAM-backed storage access
* Catalog-scoped schemas

No credentials are embedded in queries, and access is centrally managed.

---

## File Structure

```
sql/
├── validation_queries.sql      # KPI and data sanity checks
├── kpi_views.sql               # BI-facing views
├── analysis_queries.sql        # Ad-hoc analytics support
└── README.md                   # This documentation
```

*(File names may vary depending on analysis needs.)*

---

## Key Takeaways

* SQL consumes **trusted DLT outputs only**
* No duplication of ingestion or transformation logic
* Gold-layer metrics are finalized before SQL usage
* Dashboards stay clean and performant
* Governance and security are enforced centrally

This SQL layer exists to **support analytics**, not replace data engineering.

---



