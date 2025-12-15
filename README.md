NYC 911 Operational Performance Lakehouse (Databricks DLT)
Overview

This project implements a production-style Lakehouse architecture for analyzing NYC 911 emergency response performance using Databricks Delta Live Tables (DLT).
The goal is to transform raw 911 operational data into trusted, KPI-ready datasets that support executive dashboards and SLA monitoring.

The pipeline follows a Bronze → Silver → Gold data model, enforces cloud-native security via IAM & Unity Catalog, and exposes business-ready metrics such as incident volume trends, response time SLAs, and SLA breach rates.

This project was intentionally designed to mirror real enterprise data engineering patterns, not a toy or notebook-only workflow.

Architecture

High-level flow:

Raw 911 Data
   ↓
Bronze (Raw Delta Tables)
   ↓
Silver (Cleaned & Normalized)
   ↓
Gold (KPI Aggregations & BI Views)
   ↓
Databricks Dashboards


Key design principles:

Clear separation of ingestion, transformation, and analytics

Delta Lake ACID guarantees at every layer

IAM-based access (no static credentials)

BI consumes only Gold-layer views

Data Pipeline (Bronze → Silver → Gold)
🟫 Bronze Layer — Raw Ingestion

Stores raw NYC 911 operational records as Delta tables

Minimal transformations

Schema preserved for traceability and replay

Acts as the immutable system of record

🪙 Silver Layer — Cleaned & Normalized

Standardizes timestamps and agency identifiers

Removes malformed or incomplete records

Normalizes metrics required for KPI calculations

Designed for reuse across multiple analytics use cases

🥇 Gold Layer — Analytics & KPIs

Pre-aggregated, business-ready datasets

Metrics include:

Total incident volume

Average call-to-first-pickup time

Dispatch and pickup response times

SLA breach percentage

Optimized for BI and dashboard performance

All transformations are orchestrated using Delta Live Tables (DLT) for reliability, observability, and maintainability.

Security & IAM Design

This project intentionally avoids static credentials and instead uses AWS IAM + Databricks Unity Catalog for secure access.

IAM Highlights

Dedicated IAM role (nyc_911_role) for Databricks

Role assumption via STS + external ID

No access keys stored in code or notebooks

Unity Catalog & External Locations

Separate external locations for:

bronze

silver

gold

catalog

Each layer maps to a controlled S3 prefix

Access governed centrally through Unity Catalog

This mirrors enterprise-grade Databricks security patterns used in production environments.

Gold KPIs & BI Layer

All business logic is finalized in the Gold layer, ensuring dashboards remain:

Simple

Fast

Consistent

Trustworthy

Core KPIs

Total 911 Incidents (2014–Present)

Monthly Incident Volume by Agency

Average Call-to-First-Pickup Time

SLA Breach Rate (%)

This design prevents metric drift and eliminates duplicated logic across BI tools.

Dashboards

Dashboards are built directly on Gold-layer datasets and include:

📈 Monthly 911 Incident Volume (by agency)

⏱️ Average Call-to-First-Pickup Trends

🔢 Total Incident Count KPI Tile

🚨 SLA Breach Rate KPI Tile

Dashboards are filterable by agency and optimized for operational monitoring.

Important: No dashboards query Bronze or Silver tables directly.

Engineering Decisions (Why This Matters)

DLT instead of notebooks → pipeline reliability and data quality

Gold-layer KPIs → prevents BI logic sprawl

IAM + Unity Catalog → production-grade security

Layered S3 structure → scalable and auditable storage

Separation of SQL & BI logic → maintainability

These decisions reflect how modern data platforms are built at scale.

Scalability & Future Enhancements

Potential extensions:

Streaming ingestion (Auto Loader / Kafka)

Alerting on SLA breaches

Historical backfill automation

Agency-level anomaly detection

Cost optimization via Z-ORDER and clustering

The architecture is intentionally designed to support these without rework.

Tech Stack

Databricks

Delta Live Tables (DLT)

Delta Lake

Unity Catalog

AWS S3

AWS IAM (STS AssumeRole)

SQL (Analytics & BI)

Databricks Dashboards

Project Structure
nyc_911_project/
├── architecture/      # Architecture diagrams
├── dashboards/        # Dashboard screenshots
├── dlt/               # DLT pipeline definitions
├── iam/               # IAM & Unity Catalog configuration
├── sql/               # SQL transformations & views
├── screenshots/       # Supporting visuals
└── README.md

Final Notes

This project was built to demonstrate real-world data engineering practices, not just technical correctness.
It emphasizes security, scalability, maintainability, and analytics reliability — the same principles required in production environments.
