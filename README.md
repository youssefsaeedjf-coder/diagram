graph LR
    %% Tool Definitions
    subgraph Orchestration ["⚡ ORCHESTRATION & QUALITY CONTROL"]
        Airflow((Apache Airflow))
        DQE{dbt Tests / DQ Gates}
    end

    %% Phase 1: Ingestion
    subgraph Sources ["📡 SOURCE SYSTEMS"]
        direction TB
        S1[Intellect CRM/LMS - PostgreSQL]
        S2[V-Tiger - MySQL]
        S3[Future Mobile App - API/Firebase]
    end

    Sources -->|Extract & Load: Airbyte| BRONZE

    %% Phase 2: Processing
    subgraph Storage ["💾 DATA LAKEHOUSE (PostgreSQL)"]
        direction TB
        
        subgraph BRONZE ["BRONZE: STAGING"]
            STG[Raw Tables: 1-to-1 Mirrors]
        end

        subgraph SILVER ["SILVER: TRANSFORMATION FOUNDRY"]
            INT[Intermediate: CTE Logic & Heavy Joins]
            CDM[Normalized CDM: Logical Entities]
        end

        subgraph GOLD ["GOLD: ENTERPRISE WAREHOUSE"]
            EDW[SCD Type 2 & Historical Snapshots]
            Marts[Data Marts: Denormalized for PBI]
        end

        STG -->|Cleaning| INT
        INT -->|Modeling| CDM
        CDM -->|Historization| EDW
        EDW -->|Final Delivery| Marts
    end

    %% Quality & Feedback
    Airflow -.-> Sources
    Airflow -.-> STG
    STG -.-> DQE
    CDM -.-> DQE
    DQE -.-> Marts

    %% Phase 3: Consumption
    subgraph Consumption ["📊 BUSINESS INTELLIGENCE"]
        direction TB
        PBI{{Power BI: Sales & Collections}}
        ML{{Python: Early Settlement Pred.}}
        CLevel{{C-Suite Executive Dashboards}}
    end

    Marts --> PBI
    Marts --> ML
    Marts --> CLevel

    %% Styling
    classDef bronze fill:#cd7f32,stroke:#333,stroke-width:2px,color:#fff;
    classDef silver fill:#c0c0c0,stroke:#333,stroke-width:2px,color:#000;
    classDef gold fill:#ffd700,stroke:#333,stroke-width:2px,color:#000;
    class BRONZE bronze;
    class SILVER silver;
    class GOLD gold;
