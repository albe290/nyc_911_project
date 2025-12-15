

# Security & IAM Configuration

## Overview

This project implements **enterprise-grade security controls** using **AWS IAM** and **Databricks Unity Catalog** to manage access to NYC 911 Lakehouse data.

All data access is governed through **IAM role assumption and Unity Catalog external locations**, with **no static credentials, access keys, or secrets stored in code or notebooks**.

This design mirrors how Databricks Lakehouse platforms are secured in real production environments.

---

## Security Objectives

* Enforce **least-privilege access** across all data layers
* Eliminate static credentials and secret sprawl
* Centralize access control and auditing
* Secure S3 access using IAM + STS AssumeRole
* Ensure BI consumers only access **Gold-layer** data

---

## IAM Role Architecture

### Databricks Access Role

A dedicated IAM role is created to grant Databricks controlled access to Amazon S3:

```
Role name: nyc_911_role
```

This role is **assumed by Databricks** via AWS STS using an **external ID**, preventing unauthorized access and confused-deputy attacks.

---

### Trust Relationship

The role trust policy explicitly allows Databricks to assume the role:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::<ACCOUNT_ID>:role/nyc_911_role",
          "arn:aws:iam::<DATABRICKS_ACCOUNT>:role/unity-catalog-prod-UCMasterRole-*"
        ]
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "<DATABRICKS_EXTERNAL_ID>"
        }
      }
    }
  ]
}
```
<img width="1023" height="252" alt="IAM_TRUST_POLICY" src="https://github.com/user-attachments/assets/5549cbf7-c0b5-49d0-8d2c-aaa8462cc3e0" />

### Key Security Properties

* External ID enforced
* Explicit trusted principals
* No wildcard permissions
* Role assumption only (no long-lived credentials)
* Fully auditable via AWS CloudTrail

---

## S3 Lakehouse Storage Layout

The NYC 911 Lakehouse follows a **layered S3 structure**:

```
s3://nyc-911-lakehouse-usw2/
├── bronze/    # Raw ingested data
├── silver/    # Cleaned & normalized data
├── gold/      # KPI-ready analytics data
└── catalog/   # Unity Catalog managed tables
```

Each layer is governed independently through Unity Catalog.

---

## Unity Catalog External Locations

Each S3 prefix is registered as a **Unity Catalog external location** and mapped to the IAM role.

### Bronze Layer (Raw Data)

```sql
CREATE EXTERNAL LOCATION nyc911_bronze
URL 's3://nyc-911-lakehouse-usw2/bronze/'
WITH (STORAGE CREDENTIAL nyc_911_role)
COMMENT 'Bronze Delta tables for NYC 911 Lakehouse';
```

---

### Silver Layer (Cleaned & Normalized)

```sql
CREATE EXTERNAL LOCATION nyc911_silver
URL 's3://nyc-911-lakehouse-usw2/silver/'
WITH (STORAGE CREDENTIAL nyc_911_role)
COMMENT 'Silver cleaned/normalized NYC 911 Delta tables';
```

---

### Gold Layer (Analytics & KPIs)

```sql
CREATE EXTERNAL LOCATION nyc911_gold
URL 's3://nyc-911-lakehouse-usw2/gold/'
WITH (STORAGE CREDENTIAL nyc_911_role)
COMMENT 'Gold zone for NYC 911 Delta tables';
```

---

### Catalog Root

```sql
CREATE EXTERNAL LOCATION nyc911_catalog
URL 's3://nyc-911-lakehouse-usw2/catalog/'
WITH (STORAGE CREDENTIAL nyc_911_role)
COMMENT 'Catalog root for NYC 911 Lakehouse managed tables';
```

---
<img width="1267" height="555" alt="image" src="https://github.com/user-attachments/assets/89a67fc8-eab7-4a26-be4c-deb8fda5400c" />

## Validation & Auditing

External locations are validated using:

```sql
DESCRIBE EXTERNAL LOCATION nyc911_bronze;
DESCRIBE EXTERNAL LOCATION nyc911_silver;
DESCRIBE EXTERNAL LOCATION nyc911_gold;
DESCRIBE EXTERNAL LOCATION nyc911_catalog;
```

This confirms:

* Correct S3 path mapping
* Correct IAM role association
* Ownership and creation metadata
* Unity Catalog governance enforcement

---
<img width="1263" height="631" alt="Validate_external_locations" src="https://github.com/user-attachments/assets/d09a2bdc-e4b0-4238-95e0-44e61ac064bb" />

## Security Design Principles

* **Bronze, Silver, and Gold layers are isolated**
* **BI dashboards only query Gold-layer views**
* **No notebooks or dashboards access raw S3 paths**
* **All access flows through Unity Catalog**

This prevents accidental data leakage, logic duplication, and unauthorized access.

---

## Why This Matters

This security design demonstrates:

* Real-world Databricks + AWS IAM integration
* Production Unity Catalog governance patterns
* Cloud-native, auditable access control
* Enterprise Lakehouse security architecture

This is the same model used in regulated and large-scale environments.

---

## Summary

| Area           | Implementation           |
| -------------- | ------------------------ |
| Authentication | AWS STS AssumeRole       |
| Authorization  | Databricks Unity Catalog |
| Storage        | Amazon S3                |
| Secrets        | None stored              |
| Access Control | IAM + UC                 |
| Auditability   | CloudTrail               |

---

## Final Note

This IAM implementation elevates the project beyond a typical demo and showcases **production-grade security engineering** alongside data engineering.

---





