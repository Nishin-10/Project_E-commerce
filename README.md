# 🛒 End-to-End E-Commerce Data Engineering Pipeline

## 🚀 Project Overview

This project implements a production-grade data pipeline using the **Medallion Architecture** (Bronze, Silver, Gold) on **Databricks**. It processes raw e-commerce transaction data, cleans and standardizes it, and builds a Star Schema for Business Intelligence reporting.

## 🛠️ Tech Stack & Features Used

This project leverages the modern **Databricks Data Intelligence Platform**:

* **Compute:** Databricks All-Purpose Compute (PySpark)
* **Storage Format:** Delta Lake (for ACID transactions and schema enforcement)
* **Data Governance:** **Unity Catalog** (3-level namespace: `catalog.schema.table`)
* **Data Ingestion:** **Databricks Volumes** (accessing raw CSVs via `/Volumes/` path)
* **Version Control:** **Databricks Git Folders (Repos)** linked to GitHub
* **Transformation:** PySpark (Regex cleaning, Broadcast Joins, Window functions)
* **Orchestration:** Medallion Architecture (Bronze  Silver  Gold)

## 🏗️ Architecture Design

### 1. 🟤 Bronze Layer (Raw Ingestion)

* **Goal:** Ingest raw CSV files from **Databricks Volumes** with schema enforcement.
* **Features:**
* Preserved raw history.
* Added audit columns: `ingestion_timestamp` and `source_filename`.
* Used `mergeSchema` to handle upstream schema evolution.



### 2. ⚪ Silver Layer (Cleansed & Conformed)

* **Goal:** Clean data and remove data quality issues.
* **Transformations:**
* **Deduplication:** Removed duplicate orders based on composite keys.
* **Data Quality:** Fixed typos in `material` columns, handled nulls in `customer_id`.
* **Standardization:** Converted currency strings to numeric types and parsed timestamps.



### 3. 🟡 Gold Layer (Business Intelligence)

* **Goal:** consumption-ready **Star Schema** for BI tools (Power BI/Tableau).
* **Modeling:**
* **Fact Table:** `gld_fact_order_items` (Transactions).
* **Dimension Tables:** `gld_dim_products`, `gld_dim_customers`, `gld_dim_date`.
* **Business Logic:** Implemented Currency Conversion (to INR) using **Broadcast Joins**.
* **Denormalization:** Created a wide view `fact_transactions_denorm` for high-performance reporting.



## 📂 Repository Structure

```bash
Project_E-commerce/
├── 1_setup/                   # Database & Schema creation
├── 2_medallion_dimension/     # Dim tables (Brands, Products, Customers)
├── 3_medallion_fact/          # Fact tables (Order Items)
└── README.md                  # Project Documentation

```
```mermaid
graph LR
    %% -- THEME & STYLES --
    classDef default fill:#f9f9f9,stroke:#333,stroke-width:1px;
    
    %% Style for Databases/Storage (Cylinders)
    classDef storage fill:#e3f2fd,stroke:#1565c0,stroke-width:2px,color:#000;
    
    %% Style for Processing Steps (Rectangles)
    classDef process fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000;
    
    %% Style for Bronze Layer
    classDef bronze fill:#ffe0b2,stroke:#ef6c00,stroke-width:2px,color:#000;
    
    %% Style for Silver Layer
    classDef silver fill:#e0e0e0,stroke:#616161,stroke-width:2px,color:#000;
    
    %% Style for Gold Layer
    classDef gold fill:#fff9c4,stroke:#fbc02d,stroke-width:2px,color:#000;
    
    %% Style for Dashboard
    classDef dashboard fill:#c8e6c9,stroke:#2e7d32,stroke-width:2px,color:#000;

    %% -- DIAGRAM CONTENT --
    
    subgraph S1 ["📥 Ingestion"]
        direction TB
        A[("📂 Raw CSV Files")]:::storage
        B[("🧱 Databricks Volumes")]:::storage
    end

    subgraph S2 ["🟤 Bronze Layer (Raw)"]
        direction TB
        C[("Fact: Order Items")]:::bronze
        D[("Dim: Products")]:::bronze
        E[("Dim: Customers")]:::bronze
    end

    subgraph S3 ["⚪ Silver Layer (Clean)"]
        direction TB
        F("Clean & Dedup"):::process
        G("Standardize Types"):::process
        H("Handle Nulls"):::process
        
        T1[("slv_orders")]:::silver
        T2[("slv_products")]:::silver
        T3[("slv_customers")]:::silver
    end

    subgraph S4 ["🟡 Gold Layer (Aggregated)"]
        direction TB
        I("Join & Enrich"):::process
        J[("★ Fact Sales")]:::gold
        K[("Dim Product")]:::gold
        L[("Dim Customer")]:::gold
    end

    subgraph S5 ["📊 Consumption"]
        M(["📈 BI Dashboard<br/>(Sales by Region)"]):::dashboard
    end

    %% -- CONNECTIONS --
    A --> B
    B --> C & D & E
    
    C --> F --> T1
    D --> G --> T2
    E --> H --> T3
    
    T1 & T2 & T3 --> I
    I --> J & K & L
    
    J & K & L --> M
