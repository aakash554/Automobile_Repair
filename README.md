# Automobile Repair Analytics Platform - Optimized Architecture

## 📋 Project Overview

A comprehensive data analytics platform for an automobile repair business chain with **50 stores**. This project implements an **optimized medallion architecture** (Bronze-Silver-Gold) on Databricks with a **minimalist design** - combining multiple fact tables into one consolidated source and using only essential dimensions.

**Business Objective**: Track store performance, technician efficiency, customer satisfaction, and revenue metrics across multiple locations through a unified data cube that powers 10 actionable KPIs.

**Key Innovation**: **3-Table Architecture** (1 Fact + 2 Dimensions) eliminating complex joins and Cartesian products.

---

## 🏗️ Optimized Architecture

### Medallion Architecture Layers

```
Bronze Layer (Raw Data)
    ↓
Silver Layer (Cleaned & Enriched)
    ↓
Gold Layer (Optimized)
    ├── Consolidated Fact Table (1)
    ├── Essential Dimensions (2)
    └── Data Cube (1) → Powers 10 KPIs
```

### Architecture Philosophy

**Before Optimization**: 6 fact tables + 5 dimension tables = 11 tables with 10+ joins  
**After Optimization**: 1 fact table + 2 dimension tables = **3 tables** with 3 simple joins

**Benefits**:
- ✅ **Simpler queries**: No complex multi-table joins
- ✅ **Faster performance**: Pre-aggregated revenue, deduplicated estimates
- ✅ **No Cartesian products**: Perfect 1:1 grain (170,273 orders = 170,273 rows)
- ✅ **Easier maintenance**: Update one fact table instead of six
- ✅ **Single source of truth**: All KPIs query from one data cube

---

## 📊 Key Performance Indicators (10 KPIs)

1. **MTD Performance vs Previous MTD** - Monthly revenue and order trends
2. **Average Days in Shop** - Service duration by store and type
3. **Survey Coverage** - Response rates and engagement metrics
4. **Survey Scores Summary** - Customer satisfaction with rankings
5. **Revenue vs Budget** - Financial performance tracking
6. **Top Technicians** - Delivery accuracy and performance
7. **YTD Revenue Growth** - Annual growth tracking
8. **Stage-wise Cycle Time** - Process bottleneck identification
9. **Estimator Accuracy** - Cost prediction performance
10. **Technician Workload** - Resource planning and distribution

---

## 🚀 How to Run

### Execution Order

1. **Bronze Layer**: `Bronze/raw_data.ipynb` → Creates 6 raw tables
2. **Silver Layer**: Run all 6 silver notebooks in sequence
3. **Gold Dimensions**: `Gold/Data Mart/Gold_Dim.ipynb` → Cells 1-2 (dim_date, dim_store)
4. **Gold Fact**: `Gold/Data Mart/Gold_fact.ipynb` → Cell 8 (fact_orders_consolidated)
5. **Data Cube**: `Gold/Data Mart/Gold_Data_cube.ipynb` → Cell 2 (data_cube_comprehensive)
6. **KPIs**: `Gold/Data Curated/Gold_kpi.ipynb` → Creates all 10 KPI tables

---

## 🗂️ Essential Tables (3)

| Table | Type | Rows | Purpose |
|-------|------|------|---------|
| `fact_orders_consolidated` | Fact | 170,273 | All order, revenue, estimate, survey, budget data |
| `dim_date` | Dimension | 2,192 | Date attributes for time analysis |
| `dim_store` | Dimension | 50 | Store and manager information |

**Data Cube**: `data_cube_comprehensive` (170,273 rows) - Powers all 10 KPIs

---

## 🎯 Data Quality Results

| Metric | Value | Status |
|--------|-------|--------|
| Total Orders | 170,273 | ✅ |
| Unique Orders in Cube | 170,273 | ✅ Perfect 1:1 grain |
| Orders with Revenue | 124,032 (72.8%) | ✅ |
| Orders with Estimates | 123,458 (72.5%) | ✅ |
| Stores | 50 | ✅ |
| Technicians | 100 | ✅ |
| Estimators | 25 | ✅ |
| Avg Estimate Accuracy | 81.25% | ✅ |

---

## 📈 Key Transformations

### 1. Revenue Aggregation
- **Problem**: Multiple invoices per order (186k invoices → 124k orders)
- **Solution**: Pre-aggregate to `total_invoice_amount` per order

### 2. Estimate Deduplication
- **Problem**: Multiple versions per order (400k+ rows for 170k orders)
- **Solution**: Filter to `version_type = 'Final'` only

### 3. Dimension Optimization
- **Problem**: Duplicate rows in technician/estimator/service_type dimensions
- **Solution**: Fixed deduplication + embedded small dimensions in fact table

---

## 🔧 Technical Stack

- **Platform**: Databricks on AWS
- **Compute**: Serverless SQL
- **Storage**: Unity Catalog (`automobile_repair` catalog)
- **Languages**: SQL
- **Data Source**: AWS S3 CSV files
- **Architecture**: Optimized Medallion with Consolidated Fact Table

---

## 📞 Support

**Aakash Sharma**  
📱 9596924054  
📧 aakasharma5504@gmail.com

---

## 📜 Version History

### v2.0 (Current - Optimized)
- ✅ Consolidated 6 fact tables → 1 fact table
- ✅ Reduced 5 dimensions → 2 dimensions + embedded attributes
- ✅ Created unified data cube (170,273 rows)
- ✅ Expanded from 7 KPIs → 10 KPIs
- ✅ Perfect 1:1 order grain achieved
- ✅ Zero external joins in KPI queries

### v1.0 (Initial)
- Bronze-Silver-Gold architecture
- Multiple fact and dimension tables
- 7 KPI outputs






