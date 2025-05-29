# 📦 ETL Pipeline: MySQL Star Schema for Rental Analytics

## 📖 Overview

This project implements an **ETL (Extract, Transform, Load)** pipeline using Python and Pandas to build a **star schema** in a MySQL database. It extracts data from a transactional database (`filedb`), transforms it into structured dimensional and fact tables, and loads it into a data warehouse schema (`starschema`). Finally, it visualizes monthly revenue per staff.

---

## 🏗️ Architecture

- **Source DB**: `filedb` (MySQL) – Transactional rental database
- **Target DB**: `starschema` (MySQL) – Analytical star schema
- **Language**: Python (with Pandas, SQLAlchemy, Seaborn, Matplotlib)

---

## 🔄 ETL Workflow

### 1. 🔍 Extract

Data is pulled from the source (`filedb`) using `pandas.read_sql`:

- `payment` → for payment dates and amounts
- `staff` → for staff details
- `rental` → for rental transactions
- `film` → for film metadata
- `store` → for store and manager info
- `inventory` → for stock levels and update dates

---

### 2. 🧪 Transform

Transformations include:

#### 📅 `dim_date`
- Extract year, month, day, day name, month name, quarter from `payment_date`
- Generate `date_id` in `YYYYMMDD` format

#### 👨‍💼 `dim_staff`, 📼 `dim_rental`, 🎬 `dim_film`, 🏬 `dim_store`
- Selected relevant columns from source tables for dimensional modeling

#### 💰 `fact_monthly_payment`
- Join `payment` with `dim_date` to get date context
- Aggregated per staff and month

#### 📦 `fact_daily_inventory`
- Convert `last_update` to `date`
- Join with `dim_date` to assign `date_id`
- Group by `date_id`, `film_id`, `store_id` to count inventory

---

### 3. 💾 Load

- Existing fact and dimension tables in `starschema` are dropped (with FK checks disabled)
- All transformed tables are loaded to MySQL using `to_sql(..., if_exists='replace')`:
  - **Dimension Tables**: `dim_date`, `dim_staff`, `dim_rental`, `dim_film`, `dim_store`
  - **Fact Tables**: `fact_monthly_payment`, `fact_daily_inventory`

---

## 📊 Visualization

A line chart shows **monthly revenue per staff** using `Seaborn`:

- SQL joins `fact_monthly_payment` with `dim_staff`
- Revenue is aggregated per staff per month
- Plot shows trends over time for each staff member

---

## 🛠️ Requirements

- Python 3.x
- MySQL Server
- Python Packages:
  - `pandas`
  - `sqlalchemy`
  - `mysql-connector-python`
  - `matplotlib`
  - `seaborn`

---

## 🚀 How to Run

1. Update MySQL credentials in the script:
   ```python
   username = "root"
   password = "YourPassword"
