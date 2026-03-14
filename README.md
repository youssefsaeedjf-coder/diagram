### 🏗️ Jameel Finance Data Platform Architecture

```mermaid
graph LR
    %% Data Sources
    subgraph Sources ["1. Source Systems"]
        A[Intellect CRM/LMS]
        B[V-Tiger Telesales]
        C[Mobile App - Future]
    end

    %% Ingestion
    A & B & C -->|Airbyte| STG[(Staging Layer)]

    %% Transformation Pipeline
    subgraph dbt ["2. dbt Transformation Foundry"]
        direction TB
        STG --> INT[Intermediate Layer: CTEs & Joins]
        INT --> CDM[CDM Layer: Normalized Entities]
    end

    %% Storage & Analytics
    subgraph Warehouse ["3. Postgres EDW"]
        direction TB
        CDM --> EDW[EDW: SCD Type 2 & Snapshots]
        EDW --> Marts[Data Marts: Logic-Ready]
    end

    %% Consumption
    Marts --> PBI{{Power BI Dashboards}}
    Marts --> ML{{ML Models}}

    %% Orchestration
    Airflow((Airflow)) -.-> Sources
    Airflow -.-> dbt
    Airflow -.-> Warehouse
