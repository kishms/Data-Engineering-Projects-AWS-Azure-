# 📘 From API to Insights: Automated NYC Taxi Data Pipeline on Azure ( ADLS Gen2 + ADF + Databricks)

**End-to-End Data Pipeline designed and implemented by Kishore MS**  
🔗 LinkedIn: [Kishore MS](https://in.linkedin.com/in/kishore-m-s-ba56b4245)

**(NYC Taxi Dataset – API Ingestion with Parameterized Pipelines)**  

---

## 🔹 Project Overview  
This project demonstrates building a **modern Azure Data Engineering pipeline** using the **NYC Taxi Dataset**, where data is ingested directly from APIs instead of manual uploads.  

It covers:  
- API-based ingestion with **dynamic parameterized pipelines**.  
- Medallion Architecture (Bronze → Silver → Gold).  
- Secure authentication using **Service Principal** for Databricks ↔ ADLS access.  
- Transformations in Databricks with PySpark.  
- Gold layer stored as **Managed Tables** for reporting.  

---

## 📂 Dataset Source  
- **NYC Taxi & Limousine Commission (TLC) dataset** (open data).  
- Data for **Green Taxi Trips (2023)**.  
- File format: **Parquet**.  
- Supplementary CSVs uploaded manually into ADLS Bronze:  
  - `trip_type.csv`  
  - `taxi_zone.csv`  

👉 Monthly trip data → **pulled dynamically from API**.  
👉 Lookup CSVs → **uploaded manually**.  

---

## 🏗️ Architecture  
- **Source** → API + lookup CSVs.  
- **Bronze Layer (ADLS)** → Raw Parquet + lookup data.  
- **Silver Layer (Databricks)** → Cleaned & joined trip data.  
- **Gold Layer (Databricks)** → Saved as **Managed Tables** (no dimensional schema).  
- **Governance** → Unity Catalog (Metastore + External Location).  
- **Serving** → Power BI dashboards.  

---

## ⚙️ Tools & Services Used  
- Azure Data Factory (ADF) – Parameterized pipelines, API ingestion.  
- Azure Data Lake Storage Gen2 (ADLS) – Bronze, Silver, Gold zones.  
- Azure Databricks – PySpark transformations.  
- Unity Catalog – Governance and access control.  
- **Service Principal** – Secure Databricks ↔ ADLS connection.  
- Power BI – Reporting and analytics.  

---

## 🔄 Pipeline Workflow  

### 1. Resource Setup  
- Created **Resource Group** + **ADLS Gen2** with containers (Bronze, Silver, Gold).  
- Uploaded lookup CSVs (`trip_type`, `taxi_zone`) into Bronze.  

### 2. API Ingestion with ADF → Bronze  
- Created **HTTP Linked Service** for NYC Taxi API.  
- Built a **Parameterized Dataset** in ADF:  
  - Parameter for **month** (`p_month`).  
  - Used parameter in file path to dynamically fetch Parquet files.  
- Built **ADF pipeline** with:  
  - **Copy Activity** → API → ADLS Bronze.  
  - **ForEach Activity** → loop through month parameter values (`01–12`).  
  - **If Condition activity** to handle file name differences:  
    - If month `< 10` → prefix `0` (e.g., `2023-01.parquet`).  
    - If month `>= 10` → no prefix (e.g., `2023-10.parquet`).  
- ✅ Result → Automated ingestion of all 12 months dynamically with correct file paths.  

### 3. Parameterized Functions in ADF  
- Pipeline parameters created (`month`, `filePath`).  
- Functions dynamically constructed dataset URLs, for example:  

  `https://.../green_tripdata_2023-@{pipeline().parameters.month}.parquet`  

- ✅ Result → Pipeline reusable for any year/month ingestion.  

### 4. Bronze → Silver (Databricks)  
- Configured **Service Principal authentication** for secure Databricks ↔ ADLS access.  
- Read Parquet trip data + lookup CSVs from Bronze.  
- Applied transformations:  
- Standardized schema & datatypes. 
- Wrote curated outputs into Silver zone.  

### 5. Silver → Gold (Managed Tables)  
- Data promoted from Silver to Gold.  
- Stored as **Managed Tables in Databricks**.  
- No dimensional modeling — Gold is curated data ready for BI consumption.  

### 6. Governance with Unity Catalog  
- Created **Metastore**.  
- Registered **External Location** for ADLS.  
- Applied **RBAC** across layers for secure governance.  

---

## 📌 Key Functions & Features  

### 🔹 Azure Data Factory  
- Parameterized datasets (`p_month`).  
- **ForEach activity** → automated ingestion of all months.  
- **If Condition activity** → handled `< 9` vs `>= 10` month naming convention.  
- API ingestion with HTTP Linked Service.  

### 🔹 Databricks (PySpark)  
- Transformations, joins, partitioning.  
- Service Principal for ADLS access.  
- Gold layer as **Managed Tables**.  

### 🔹 Governance (Unity Catalog)  
- Metastore for metadata.  
- External Locations for ADLS mapping.  
- RBAC + lineage enforcement.  

---

## 📸 Screenshots  

*Creating medallion arch*

![alt text](<Screenshots/Screenshot 2025-09-07 at 9.59.37 PM.png>)

*Linked service for connecting to HTTPS through API*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.16.38 PM.png>)

*Creating a For Each activity (for loop) ranging from 1 to 12*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.01.55 PM.png>)

*If-Else condition inside For Each activity*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.00.15 PM.png>)

*When the if condition is true*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.00.41 PM.png>)

*When the if condition is false*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.08.57 PM.png>)

*Sink folder navigation*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.01.26 PM.png>)

*All the data files are copied to the destination with no manual effort and full automation.*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.18.42 PM.png>)

*Accessing the data residing in ADLS Gen2 from Databricks using Service Principal*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.02.34 PM.png>)

*Performing basic visualization using the display function of Databricks*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.03.19 PM.png>)

*Data moved to the Gold Layer as a managed table*

![alt text](<Screenshots/Screenshot 2025-09-07 at 10.04.10 PM.png>)

> ⚠️ **Note:** Screenshots of Silver and Gold layer transformations are not included here, as the detailed steps are available in the attached **Databricks Notebooks** within the repository.

---

## 🎯 Key Takeaways  
- Built an **automated parameterized pipeline** for API ingestion.  
- Handled file naming variations with **If Condition logic**.  
- Implemented secure Databricks ↔ ADLS access with **Service Principal**.  
- Delivered **Bronze → Silver → Gold pipeline** with Gold as Managed Tables.  
- Integrated **Power BI dashboards** for reporting.  

---

## 🧑‍💻 Recruiter Note  
This project highlights my ability to build **automated, parameterized data pipelines** using APIs in Azure Data Factory, integrate them securely with Databricks via **Service Principals**, and manage data lifecycle across **Bronze, Silver, and Gold layers**. It also demonstrates hands-on experience with **Unity Catalog for governance** and **Managed Tables for curated reporting** — skills directly applicable to enterprise Data Engineering roles.  
