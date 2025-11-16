# Cloud Data Warehouse (AWS S3 + Snowflake) -- Bronze/Silver/Gold Architecture

## 📌 Overview

This project implements a complete **cloud-native data warehouse**
using:

-   **Amazon S3** → Data lake storage
-   **Snowflake** → Cloud data warehouse
-   **Bronze/Silver/Gold** layered architecture
-   **COPY INTO** ingestion
-   **Dimensional modelling** (facts + dimensions)
-   **Data quality validation**

This README explains the cloud architecture while **masking all
sensitive account details** for security.

------------------------------------------------------------------------

## 🏗 Architecture Summary

### **1️⃣ Data Source**

CSV files stored in S3:

    s3://<masked-bucket-name>/datasets/

### **2️⃣ Bronze Layer**

Raw landing tables loaded directly from an external Snowflake stage.

### **3️⃣ Silver Layer**

Cleansed, transformed, standardized data: - Date conversions\
- Gender + marital status mapping\
- Product dimension parsing\
- Sales/price recalculation\
- Country normalization

### **4️⃣ Gold Layer**

Analytics-ready star schema: - `DIM_CUSTOMERS`\
- `DIM_PRODUCTS`\
- `FACT_SALES`

------------------------------------------------------------------------

## 🚀 Implemented Components

### **Snowflake**

  Type                  Name
  --------------------- ----------------------------
  Database              `DATAWAREHOUSE`
  Schemas               `BRONZE`, `SILVER`, `GOLD`
  Storage Integration   `S3_INT`
  Stage                 `BRONZE_STAGE`
  File Format           `CSV_STD`

### **AWS**

  Service      Purpose
  ------------ ------------------------------------------------
  S3 Bucket    Raw data lake
  IAM Role     Allows Snowflake to read S3 (masked ARN below)
  IAM Policy   Read-only S3 policy for Snowflake

------------------------------------------------------------------------

## 🪣 Snowflake ↔ S3 Integration (Masked)

### IAM Role ARN 

    arn:aws:iam::<AWS-ACCOUNT-ID-MASKED>:role/snowflake_access_role

### Trusted Snowflake Principal 

    arn:aws:iam::<SNOWFLAKE-ACCOUNT-ID-MASKED>:user/<masked-user-name>

### External ID 

    DR45386_SFCRole=<masked-token>

### Allowed S3 Location 

    s3://<masked-bucket-name>/datasets/

All values required for IAM trust and stage integration have been
intentionally anonymized.

------------------------------------------------------------------------

## 📂 Repository Structure

    ├── 01_bronze_layer_snowflake.sql
    ├── 02_silver_layer_snowflake.sql
    ├── 03_gold_layer_snowflake.sql
    └── README.md

------------------------------------------------------------------------

## 🧪 Data Quality Checks

Silver and Gold layers include validations for:

✔ Duplicate keys
✔ Surrogate key uniqueness
✔ Invalid dates
✔ Negative or missing financial values
✔ Product date range issues
✔ Fact-table orphan checks

All implemented using Snowflake SQL.

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

## ✔ Summary

This project demonstrates:

-   Secure cloud data ingestion
-   Proper IAM role delegation (masked in this README)
-   Snowflake ELT transformations
-   Bronze/Silver/Gold architecture
-   Analytics-ready star schema
-   Production-style data quality checks

All sensitive identifiers and account numbers have been masked for
security.

------------------------------------------------------------------------

## 📬 Contact

Feel free to reach out if you want help extending this into: - Automated
pipelines (Airflow / Glue) - BI Dashboards (Power BI / QuickSight) -
Documentation / diagrams
