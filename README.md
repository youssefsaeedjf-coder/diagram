# 🏦 Jameel Finance: Enterprise Data Platform (EDP)
> **Modern Data Stack:** Airbyte ⮕ Postgres ⮕ dbt ⮕ Airflow ⮕ Power BI

This repository contains the end-to-end transformation logic for Jameel Finance. We have transitioned from a legacy "Full-Refresh" reporting model to a high-governance **Medallion Architecture**, ensuring financial data integrity, auditability, and C-Suite level performance.

---

## 🏗️ Architecture Blueprint

```mermaid
graph LR
    subgraph "1. SOURCES"
        S1[Intellect CRM/LMS]
        S2[V-Tiger Telesales]
    end

    subgraph "2. INGESTION (Airbyte)"
        STG[(Staging Layer)]
    end

    subgraph "3. TRANSFORMATION (dbt)"
        direction TB
        INT[Intermediate: Logic Foundry]
        CDM[CDM: Normalized Entities]
        EDW[EDW: History & Snapshots]
    end

    subgraph "4. CONSUMPTION"
        Marts[(Data Marts)]
        PBI{{Power BI}}
    end

    S1 & S2 -->|Extract| STG
    STG -->|Modular Joins| INT
    INT -->|Normalization| CDM
    CDM -->|SCD Type 2| EDW
    EDW -->|Star Schema| Marts
    Marts --> PBI

    style STG fill:#cd7f32,color:#fff
    style CDM fill:#c0c0c0,color:#000
    style Marts fill:#ffd700,color:#000
