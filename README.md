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
