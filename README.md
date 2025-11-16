# Cloud Data Warehouse (AWS S3 + Snowflake) -- Bronze/Silver/Gold Architecture

## 📌 Overview

This project implements a complete **cloud-native data warehouse**
using:

-   **Amazon S3** → Data lake storage\
-   **Snowflake** → Data warehouse\
-   **Bronze/Silver/Gold** layered architecture\
-   **Snowpipe / COPY INTO** for ingestion\
-   **Dimensional modelling (facts + dimensions)**\
-   **Data quality validation**

The warehouse ingests raw CRM & ERP datasets from S3, processes them in
Snowflake, and exposes analytics-ready data models for BI & reporting.

------------------------------------------------------------------------

## 🏗 Architecture Summary

### **1️⃣ Data Source**

CSV files stored in:

    s3://datawarehousesourcefiles/datasets/

### **2️⃣ Bronze Layer (Raw Ingestion)**

-   Raw CRM & ERP data loaded as-is into Snowflake BRONZE tables.
-   Loaded via:

``` sql
COPY INTO <table> FROM @BRONZE_STAGE/<path>;
```

### **3️⃣ Silver Layer (Cleansed + Conformed)**

-   Data standardised & cleaned:
    -   Date parsing\
    -   Gender and marital status mapping\
    -   Product dimension parsing\
    -   Price & sales recalculation logic\
    -   Country standardisation\
-   Produced via SQL transformations.

### **4️⃣ Gold Layer (Analytics Star Schema)**

-   **DIM_CUSTOMERS**
-   **DIM_PRODUCTS**
-   **FACT_SALES**

Gold views power BI dashboards and reporting.

------------------------------------------------------------------------

## 🚀 Implemented Objects

### **Snowflake Objects**

  Type                  Name
  --------------------- ----------------------------
  Database              `DATAWAREHOUSE`
  Schemas               `BRONZE`, `SILVER`, `GOLD`
  Storage Integration   `S3_INT`
  Stage                 `BRONZE_STAGE`
  File Format           `CSV_STD`
  Views                 Gold layer star schema

### **AWS Components**

  Service      Purpose
  ------------ ---------------------------------
  S3 Bucket    Store raw datasets
  IAM Role     Allow Snowflake to read from S3
  IAM Policy   `SnowflakeS3ReadAccessPolicy`

------------------------------------------------------------------------

## 📂 Folder Structure

    ├── 01_bronze_layer_snowflake.sql
    ├── 02_silver_layer_snowflake.sql
    ├── 03_gold_layer_snowflake.sql
    └── README.md

------------------------------------------------------------------------

## 🪣 S3 → Snowflake Integration Summary

### IAM Role ARN

    arn:aws:iam::650251713889:role/snowflake_access_role

### Allowed S3 Location

    s3://datawarehousesourcefiles/datasets/

### Trusted Snowflake Principal

    arn:aws:iam::501235162090:user/ksc91000-s

### External ID (Snowflake)

    DR45386_SFCRole=2_ftYruU+tzTE9OdeQ3NzNRq5/mhw=

------------------------------------------------------------------------

## 🧪 Data Quality Checks

Quality checks implemented for:

✔ Duplicate keys\
✔ Invalid dates\
✔ Negative or missing financial fields\
✔ Surrogate key uniqueness\
✔ Fact ↔ dimension referential integrity\
✔ Product date range validation

Scripts are included for full QA testing.

------------------------------------------------------------------------

## 📊 Example Analytics Queries

### Top customers by revenue

``` sql
SELECT 
  C.FIRST_NAME, C.LAST_NAME, C.COUNTRY,
  SUM(F.SALES_AMOUNT) AS total_sales
FROM GOLD.FACT_SALES F
JOIN GOLD.DIM_CUSTOMERS C ON F.CUSTOMER_KEY = C.CUSTOMER_KEY
GROUP BY 1,2,3
ORDER BY total_sales DESC
LIMIT 10;
```

### Sales by product category

``` sql
SELECT 
  P.CATEGORY, P.SUBCATEGORY,
  SUM(F.SALES_AMOUNT) AS total_sales
FROM GOLD.FACT_SALES F
JOIN GOLD.DIM_PRODUCTS P ON F.PRODUCT_KEY = P.PRODUCT_KEY
GROUP BY 1,2
ORDER BY total_sales DESC;
```

------------------------------------------------------------------------

## ✔ Final Notes

This cloud data warehouse demonstrates:

-   Real-world ELT pipelines\
-   Cloud-based ingestion\
-   Standardised data layers\
-   Dimensional modelling\
-   Secure S3 ↔ Snowflake integration\
-   Production-grade data quality checks

Perfect for showcasing **Data Engineering / Cloud Engineering** skills.

------------------------------------------------------------------------

## 📬 Contact

If you'd like help extending this into: - BI dashboards (Power BI /
QuickSight) - Orchestrated pipelines (Airflow / AWS Glue) - Data lineage
or documentation

Feel free to reach out!
