# Automobile Repair Analytics Platform

## 📋 Project Overview

A comprehensive data analytics platform for an automobile repair business chain with 50 stores. This project implements a medallion architecture (Bronze-Silver-Gold) on Databricks to process operational data and deliver actionable KPIs for business decision-making.

**Business Objective**: Track store performance, technician efficiency, customer satisfaction, and revenue metrics across multiple locations to optimize operations and improve service quality.

---

## 🏗️ Architecture

### Medallion Architecture Layers

```
Bronze Layer (Raw Data)
    ↓
Silver Layer (Cleaned & Enriched)
    ↓
Gold Layer
    ├── Data Mart (Dimensional Model)
    └── Data Curated (KPI Tables)
```

### Layer Details

#### **Bronze Layer** (`automobile_repair.bronze`)
- **Purpose**: Raw data ingestion from CSV files stored in S3
- **Data Quality**: No transformations, preserves source data as-is
- **Tables**: 
  - `invoice` - Invoice records with amounts and dates
  - `order` - Work orders with service details
  - `estimate` - Cost estimates for repairs
  - `store` - Store and manager information
  - `ns_budget` - Monthly budget targets by store
  - `customer_survey` - Customer satisfaction survey responses

#### **Silver Layer** (`automobile_repair.silver`)
- **Purpose**: Cleaned, deduplicated, and enriched data ready for analytics
- **Transformations**:
  - Deduplication using ROW_NUMBER() window functions
  - Data type conversions and standardization
  - Date parsing and validation
  - NULL handling and data quality checks
  - Foreign key enrichment through joins
  - Derived columns (e.g., time dimensions, calculated fields)

#### **Gold Layer** (`automobile_repair.gold`)

**Data Mart (Dimensional Model)**:
- `dim_store` - Store dimension with manager details
- `dim_technician` - Technician dimension
- `fact_revenue` - Revenue fact table
- `fact_order_details` - Order details fact table

**Data Curated (KPI Tables)**:
- 7 pre-aggregated KPI tables optimized for reporting and dashboards

---

## 📊 Key Performance Indicators (KPIs)

### 1. **MTD Performance vs Previous MTD**
- **Table**: `kpi_mtd_performance`
- **Metrics**: Month-to-date revenue and completed orders compared with previous month
- **Granularity**: Store, Manager
- **Use Case**: Track monthly performance trends and identify underperforming locations

### 2. **Average Days in Shop**
- **Table**: `kpi_avg_days_in_shop`
- **Metrics**: Average vehicle service duration
- **Granularity**: Store, Service Type
- **Use Case**: Optimize service efficiency and capacity planning

### 3. **Survey Coverage**
- **Table**: `kpi_survey_coverage`
- **Metrics**: Survey sent rate and response rate
- **Granularity**: Store
- **Use Case**: Measure customer engagement and feedback collection effectiveness

### 4. **Survey Scores Summary**
- **Table**: `kpi_survey_scores`
- **Metrics**: Customer satisfaction ratings (delivery time, quality, cleanliness, communication, overall)
- **Granularity**: Store
- **Use Case**: Identify service quality issues and best practices

### 5. **Revenue vs Budget**
- **Table**: `kpi_revenue_vs_budget`
- **Metrics**: Actual revenue vs budget targets with variance analysis
- **Granularity**: Manager, Month
- **Use Case**: Financial performance tracking and manager accountability

### 6. **Top Technicians by Completion Time Accuracy**
- **Table**: `kpi_top_technicians`
- **Metrics**: Delivery accuracy and on-time completion rates
- **Granularity**: Store, Technician (Top 5 per store)
- **Use Case**: Recognize high performers and identify training needs

### 7. **Year-to-Date Revenue Growth**
- **Table**: `kpi_ytd_revenue_growth`
- **Metrics**: YTD revenue vs previous year with growth percentage
- **Granularity**: Store
- **Use Case**: Annual performance tracking and strategic planning

---

## 📁 Folder Structure

```
Automobile_Repair/
│
├── Bronze/
│   └── raw_data.ipynb                    # S3 CSV ingestion
│
├── Silver/
│   ├── silver_invoice.ipynb              # Invoice cleaning & enrichment
│   ├── silver_order.ipynb                # Order processing
│   ├── silver_estimate.ipynb             # Estimate standardization
│   ├── silver_store.ipynb                # Store dimension
│   ├── silver_ns_budget.ipynb            # Budget data processing
│   └── silver_customer_survey.ipynb      # Survey data cleaning
│
├── Gold/
│   ├── Data Mart/
│   │   ├── Gold_Dim.ipynb               # Dimension tables (store, technician)
│   │   └── Gold_fact.ipynb              # Fact tables (revenue, order details)
│   │
│   └── Data Curated/
│       └── Gold_kpi.ipynb               # All 7 KPI table definitions
│
└── README.md                             # This file
```

---

## 🔧 Technical Stack

- **Platform**: Databricks on AWS
- **Compute**: Serverless SQL/Python clusters
- **Storage**: Unity Catalog (`automobile_repair` catalog)
- **Languages**: SQL (primary), Python (optional for advanced transformations)
- **Data Source**: AWS S3 CSV files
- **Architecture Pattern**: Medallion (Bronze-Silver-Gold)

---

## 🗂️ Data Schema

### Bronze Tables (Raw)
| Table | Key Columns | Description |
|-------|-------------|-------------|
| `invoice` | invoice_id, order_id, invoice_date, invoice_amount | Payment records |
| `order` | order_id, store_id, technician_id, service_type, vehicle_in/out_datetime | Work orders |
| `estimate` | estimate_id, order_id, estimate_amount | Cost estimates |
| `store` | store_id, store_name, manager_id, manager_name | Store master data |
| `ns_budget` | ns_store_id, month, budget_amount | Monthly budget targets |
| `customer_survey` | survey_id, order_id, ratings, comments | Customer feedback |

### Silver Tables (Cleaned)
- Same structure as Bronze but with:
  - Deduplicated records
  - Validated data types
  - NULL handling
  - Enriched with foreign key attributes
  - Derived time dimensions (year, month)
  - Calculated fields (e.g., days_in_shop, delivery_accuracy)

### Gold Tables (Analytics-Ready)

**Dimensions:**
- `dim_store`: store_key, store_id, store_name, manager_id, manager_name
- `dim_technician`: technician_key, technician_id, technician_name

**Facts:**
- `fact_revenue`: invoice_id, order_id, store_id, invoice_date, invoice_amount, time dimensions
- `fact_order_details`: order_id, store_id, technician_id, service_type, dates, metrics

---

## 🚀 How to Run

### Prerequisites
- Databricks workspace access
- Unity Catalog enabled with `automobile_repair` catalog
- S3 access credentials configured
- CSV source files available in S3

### Execution Order

**Step 1: Bronze Layer**
```
Run: Bronze/raw_data.ipynb
Creates: automobile_repair.bronze.* tables
```

**Step 2: Silver Layer** (run in any order)
```
Run: Silver/silver_store.ipynb
Run: Silver/silver_invoice.ipynb
Run: Silver/silver_order.ipynb
Run: Silver/silver_estimate.ipynb
Run: Silver/silver_ns_budget.ipynb
Run: Silver/silver_customer_survey.ipynb
```

**Step 3: Gold Layer - Data Mart**
```
Run: Gold/Data Mart/Gold_Dim.ipynb
Run: Gold/Data Mart/Gold_fact.ipynb
```

**Step 4: Gold Layer - KPIs**
```
Run: Gold/Data Curated/Gold_kpi.ipynb
Creates all 7 KPI tables
```

### Query KPI Results
```sql
-- Example: View MTD performance
SELECT * FROM automobile_repair.gold.kpi_mtd_performance
ORDER BY mtd_revenue DESC;

-- Example: Check survey scores
SELECT * FROM automobile_repair.gold.kpi_survey_scores
ORDER BY avg_overall_satisfaction DESC;
```

---

## 📈 Data Transformations

### Key Transformation Logic

**Deduplication Pattern (Silver Layer)**:
```sql
WITH deduplicated AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY {id_column} ORDER BY {timestamp} DESC) as rn
    FROM bronze.{table}
)
SELECT * FROM deduplicated WHERE rn = 1
```

**Enrichment Pattern**:
- Silver tables join Bronze tables to bring in dimensional attributes
- Example: Invoice enriched with store_id → manager_id, manager_name

**Time Dimensions**:
- Extract YEAR(), MONTH() from date columns for time-based aggregations

**Calculated Metrics**:
- `days_in_shop` = DATEDIFF(vehicle_out_datetime, vehicle_in_datetime)
- `delivery_accuracy` = percentage of orders completed on promised date
- `budget_variance` = actual_revenue - budget_amount

---

## 🎯 Data Quality

### Validation Rules (Silver Layer)
- Remove records with NULL primary keys
- Deduplicate based on business logic (latest record wins)
- Validate date formats using TRY_TO_DATE()
- Handle NULL amounts (exclude or default to 0)
- Standardize string fields (trim, case normalization)

### Current Limitations
- No automated data quality monitoring
- No anomaly detection
- No referential integrity enforcement

---

## 🔄 Future Enhancements

### High Priority
1. **Orchestration**: Implement Databricks Workflows with task dependencies
2. **Optimization**: 
   - Partition fact tables by date (invoice_date, order_date)
   - Z-order on frequently filtered columns (store_id, manager_id)
   - Add OPTIMIZE and VACUUM commands
3. **Incremental Processing**: Change from full refresh to incremental loads
4. **Data Quality Framework**: Add automated validation and monitoring

### Medium Priority
- Dashboard development for KPI visualization
- Alerting for budget variances and service delays
- Historical trend analysis with slowly changing dimensions
- Predictive analytics for revenue forecasting

### Low Priority
- Real-time streaming ingestion
- Machine learning models for customer churn prediction
- Natural language query interface

---

## 👥 Stakeholders

- **Store Managers**: Daily operations and local performance
- **Regional Managers**: Multi-store oversight
- **Finance Team**: Revenue tracking and budget management
- **Operations Team**: Service efficiency and capacity planning
- **Customer Service**: Survey feedback and satisfaction metrics

---

## 📝 Naming Conventions

### Schema Naming
- `automobile_repair.bronze.*` - Raw ingested data
- `automobile_repair.silver.*` - Cleaned business data
- `automobile_repair.gold.*` - Analytics-ready tables

### Table Naming
- **Dimensions**: `dim_{entity}` (e.g., dim_store, dim_technician)
- **Facts**: `fact_{subject}` (e.g., fact_revenue, fact_order_details)
- **KPIs**: `kpi_{metric_name}` (e.g., kpi_mtd_performance)

### Column Naming
- Use snake_case for all column names
- Suffix with `_id` for identifiers
- Suffix with `_key` for dimension surrogate keys
- Suffix with `_date`, `_datetime`, `_timestamp` for temporal fields
- Suffix with `_amount` for monetary values
- Suffix with `_flag` for boolean indicators (0/1)

---

## 📞 Support

-9596924054 || Aakash Sharma

---

## 📜 Version History

- **v1.0** (Current): Initial implementation with 7 KPIs
  - Bronze-Silver-Gold architecture
  - 6 source tables ingested
  - 7 KPI outputs delivered
  - Manual notebook execution
