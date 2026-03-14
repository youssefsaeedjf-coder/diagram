# 🏦 Jameel Finance: Enterprise Data Platform (EDP)
> **Engineering Pattern:** Medallion Lakehouse Architecture (Bronze/Silver/Gold)

This repository contains the end-to-end transformation logic for Jameel Finance. By moving away from legacy "Full-Refresh" scripts to a governed dbt framework, we ensure 100% auditable financial metrics.

---

## 🏗️ Technical Architecture Blueprint

```mermaid
graph LR
    %% Orchestration & Quality
    subgraph Control ["⚡ CONTROL PLANE"]
        direction TB
        Airflow((Apache Airflow))
        DQE{dbt Cloud / DQ Gates}
    end

    %% Data Sources
    subgraph Sources ["📡 MULTI-SOURCE INGESTION"]
        direction TB
        S1[Intellect CRM/LMS - PostgreSQL]
        S2[V-Tiger - MySQL]
        S3[Mobile App - Future Streaming]
    end

    %% The Pipeline
    Sources -->|Airbyte / CDC| BRONZE

    subgraph Warehouse ["💾 DATA STORAGE (Postgres Lakehouse)"]
        direction TB
        
        subgraph BRONZE ["BRONZE: LANDING & STAGING"]
            STG[Raw Tables: 1-to-1 Mirrors]
        end

        subgraph SILVER ["SILVER: THE LOGIC FOUNDRY"]
            INT[Intermediate: Heavy Joins & Unions]
            CDM[Normalized CDM: Logical Entities]
        end

        subgraph GOLD ["GOLD: ENTERPRISE WAREHOUSE"]
            EDW[SCD Type 2 & Snapshots]
            Marts[Data Marts: Logic-Ready]
        end

        STG -->|Cleaning| INT
        INT -->|Modeling| CDM
        CDM -->|Historization| EDW
        EDW -->|Semantic Layer| Marts
    end

    %% Connections
    Airflow -.-> Sources
    Airflow -.-> Warehouse
    DQE -.-> CDM
    DQE -.-> Marts

    %% Output
    subgraph Delivery ["📊 CONSUMPTION LAYERS"]
        PBI{{Power BI: Sales & Collections}}
        ML{{Python: Early Settlement Pred.}}
    end

    Marts --> PBI
    Marts --> ML

    %% Style Classes (Making it look professional)
    classDef bronze fill:#EAD1DC,stroke:#A64D79,stroke-width:2px;
    classDef silver fill:#D0E2FF,stroke:#0043CE,stroke-width:2px;
    classDef gold fill:#FFF2CC,stroke:#D6B656,stroke-width:2px;
    class BRONZE bronze;
    class SILVER silver;
    class GOLD gold;
