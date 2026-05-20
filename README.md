# Global Education Data Warehouse Pipeline

A Data Engineering portfolio project that demonstrates an end-to-end ETL (Extract, Transform, Load) workflow using Python, Pandas, PostgreSQL, and Supabase.

This project transforms raw global education data into a structured Star Schema data warehouse for analytical purposes.

---

# Project Overview

The goal of this project is to:

- Extract raw education data from CSV
- Clean and transform the dataset
- Build a dimensional data warehouse model
- Load transformed data into PostgreSQL (Supabase)
- Reapply Primary Key (PK) and Foreign Key (FK) constraints
- Prepare the database for dashboarding and analytics tools such as Power BI or Tableau

---

# Architecture

```text
CSV Dataset
     ↓
Python + Pandas ETL
     ↓
Star Schema Modeling
     ↓
PostgreSQL (Supabase)
     ↓
Power BI / Tableau Dashboard
````

---

# Tech Stack

* Python
* Pandas
* SQLAlchemy
* PostgreSQL
* Supabase
* Jupyter Notebook

---

# Dataset

Dataset:

* Global Education Dataset

Topics included:

* Literacy Rate
* Education Enrollment
* Proficiency Scores
* School Attendance
* Birth Rate
* Unemployment Rate

---

# ETL Process

## 1. Extract

The raw CSV dataset is loaded using Pandas.

```python
df_raw = pd.read_csv('Global_Education.csv', encoding='latin1')
```

---

## 2. Transform

Data cleaning process:

* Remove duplicate records
* Remove null values
* Normalize column names
* Generate surrogate keys

```python
df_cleaned = df_raw.drop_duplicates().dropna()
```

---

## 3. Dimensional Modeling

The dataset is transformed into a Star Schema consisting of:

### Dimension Table

* dim_country

### Fact Table

* fact_global_education_metrics

---

# Entity Relationship Diagram (ERD)


![ERD](Edu_global_data_erd.drawio.png)


Recommended folder structure:

```text
project/
│
├── images/
│   └── erd.png
│
├── notebook/
│   └── global_education_etl.ipynb
│
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

---

# Data Warehouse Schema

## Dimension Table: dim_country

Stores country-level information.

| Column Name  | Description       |
| ------------ | ----------------- |
| country_id   | Primary key       |
| country_name | Country name      |
| latitude     | Country latitude  |
| longitude    | Country longitude |

---

## Fact Table: fact_global_education_metrics

Stores measurable global education metrics.

| Column Name                         | Description                         |
| ----------------------------------- | ----------------------------------- |
| fact_id                             | Primary key                         |
| country_id                          | Foreign key referencing dim_country |
| unemployment_rate                   | Country unemployment rate           |
| birth_rate                          | Country birth rate                  |
| gross_primary_education_enrollment  | Primary education enrollment        |
| gross_tertiary_education_enrollment | Tertiary education enrollment       |
| literacy_rate                       | Literacy-related metrics            |
| proficiency_scores                  | Reading and math proficiency        |
| attendance_metrics                  | Out-of-school rate metrics          |

---

# Python ETL Workflow

## Create Dimension Table

```python
dim_country = df_cleaned[
    ['country_id', 'Countries and areas', 'Latitude ', 'Longitude']
].drop_duplicates().reset_index(drop=True)
```

---

## Create Fact Table

```python
fact_global_education_metrics = df_cleaned.copy()
```

---

## Normalize Column Names

```python
fact_global_education_metrics.columns = (
    fact_global_education_metrics.columns
    .str.lower()
    .str.replace(' ', '_')
)
```

---

# Load Process

Data is loaded into Supabase PostgreSQL using SQLAlchemy.

```python
dim_country.to_sql(
    'dim_country',
    con=engine,
    if_exists='replace',
    index=False
)
```

```python
fact_global_education_metrics.to_sql(
    'fact_global_education_metrics',
    con=engine,
    if_exists='replace',
    index=False
)
```

---

# Why PK and FK Need to Be Recreated

Because the project uses:

```python
if_exists='replace'
```

Pandas will:

* Drop existing tables
* Recreate tables automatically

As a result:

* Primary Keys (PK)
* Foreign Keys (FK)
* Constraints

will be removed and must be recreated manually.

---

# Recreate Primary Key and Foreign Key

Run the following SQL query inside Supabase SQL Editor after the ETL process finishes.

```sql
-- Recreate Primary Keys
ALTER TABLE dim_country 
ADD PRIMARY KEY (country_id);

ALTER TABLE fact_global_education_metrics 
ADD PRIMARY KEY (fact_id);

-- Recreate Foreign Key
ALTER TABLE fact_global_education_metrics 
ADD CONSTRAINT fk_country 
FOREIGN KEY (country_id) 
REFERENCES dim_country(country_id);
```

---

# Environment Variables

Store database credentials securely using `.env`.

Example:

```env
DATABASE_URL=postgresql+psycopg2://USERNAME:PASSWORD@HOST:6543/postgres
```

---

# Installation

Clone the repository:

```bash
git clone https://github.com/your-username/global-education-datawarehouse.git
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Run the ETL notebook or Python script.

---

# Dashboard Possibilities

This data warehouse can be connected to:

* Power BI
* Tableau
* Looker Studio

Potential dashboards:

* Global Literacy Analysis
* Education Enrollment Trends
* Reading & Math Proficiency
* School Attendance Metrics
* Birth Rate vs Education Metrics

---

# Future Improvements

* Add Airflow orchestration
* Add dbt transformation layer
* Create automated ETL scheduling
* Add data quality validation
* Build interactive BI dashboards

---

# Author

Nugraha Adiputra

```
```
