# 🔶 AWS Medallion Architecture – Financial Transactions Pipeline  
**Real-Time CDC + AWS Glue Transformations + Automated Notifications**

---

## 📖 Overview  
This project implements an **end-to-end AWS Data Engineering pipeline** using the **Financial Transactions Dataset (Kaggle)**.  
The pipeline ingests, processes, and models transactional data following the **Medallion Architecture (Bronze → Silver → Gold)** with **Change Data Capture (CDC)** replication to an Amazon RDS PostgreSQL instance.

Key features:

- **Kafka (Local)** producer/consumer to simulate real-time data ingestion.  
- **S3 Bronze/Silver/Gold** zones for raw, cleansed, and dimensional data.  
- **AWS Lambda + SNS** for automatic KPI validation and notifications.  
- **AWS Glue (PySpark)** for transformations.  
- **Amazon RDS + AWS DMS** for canonical storage and CDC to downstream targets.

---

## 📂 Dataset  
**Financial Transactions Dataset: Analytics (Kaggle)**  
Simulated credit/debit transactions containing fields such as:

- `transaction_id`
- `account_id`
- `amount`
- `currency`
- `merchant`
- `timestamp`
- `category`  

This dataset was used to demonstrate streaming ingestion, cleansing, and dimensional modeling.

---

## 🏗️ Architecture  

**Local Kafka Producer → Local Kafka Consumer → S3 Bronze → Lambda + SNS → Glue Silver → Glue Gold → Amazon RDS → AWS DMS (CDC) → Target S3 / Downstream DB**



---

## ⚙️ Services & Components  

| Layer           | Service / Tool              | Purpose                                             |
|-----------------|----------------------------|-----------------------------------------------------|
| Ingestion       | Kafka (local terminal)      | Real-time event streaming into pipeline             |
| Storage         | Amazon S3 (Bronze, Silver, Gold) | Data lake zones                                     |
| Trigger / KPI   | AWS Lambda + Amazon SNS     | Automatic KPI validation & notifications            |
| Processing      | AWS Glue (PySpark Notebooks) | Cleansing, enrichment, dimensional modeling         |
| Canonical DB    | Amazon RDS PostgreSQL       | Stores curated transactional data                   |
| Replication     | AWS DMS                     | Full load + CDC from RDS to target S3/DB            |

---

## 🔄 Pipeline Workflow  

### 1️⃣ Kafka Ingestion → S3 Bronze  
- A **Python Kafka Producer** running locally generates streaming financial transactions.  
- A **Kafka Consumer** (also local) subscribes to the topic and writes the raw data to **S3 Bronze**.  

### 2️⃣ Bronze Zone → Lambda + SNS  
- **S3 Event Notification** triggers **AWS Lambda** and **SNS** when a new file lands in Bronze.  
- Lambda performs **KPI validation/reconciliation**.

### 3️⃣ Silver Zone – Glue Transformations  
- **AWS Glue Notebook / Job** reads Bronze data from S3.  
- Performs cleansing, deduplication, and normalization.  
- Writes curated **Parquet** data to **S3 Silver**.  


### 4️⃣ Gold Zone – Dimensional Modeling  
- **Glue Gold Job** consumes Silver tables.  
- **Glue Crawler runs on Silver layer data** at this stage to fetch the schema for dimensional modeling.  
*(Note: The Glue Crawler does not run on the Silver layer directly. Instead, it runs on the Silver data at the Gold stage to obtain schema and support Star Schema modeling.)*
- Builds a **financial dimensional model (Star Schema)**:
  - `dim_transactions`
  - `dim_accounts`
  - `dim_merchants`
  - `dim_time`
- Writes **Parquet** tables to **S3 Gold** and updates the Glue Catalog.

### 5️⃣ RDS + DMS CDC  
- Data is loaded into **Amazon RDS PostgreSQL** using **pgAdmin**.  
- **AWS DMS** configured for **Full Load + CDC**:
  - **Source:** RDS PostgreSQL.  
  - **Target:** S3 bucket or downstream database.  
  - Replicates only new/incremental changes after initial load.

---

## 📸 Screenshots 

***Files saved in local storage.***
![alt text](<Screenshots/Files in Mac.png>)

***Creating Medallion Architecture folders in S3.***
![alt text](<Screenshots/Creating folders in S3.png>)

***Creating an IAM role for Kafka-S3.***
![alt text](<Screenshots/Creating IAM.png>)

***Creating an SNS event notification for Bronze layer file ingestion.***
![alt text](<Screenshots/bronze sns event triggered.png>)

***Creating an SNS event notification for Gold layer file ingestion.***
![alt text](<Screenshots/sns for gold.png>)

***Creating topics in Apache Kafka.***
![alt text](<Screenshots/Creating kafka topics.png>)

***Apache Kafka producer has successfully written the data to  the cluster.***
![alt text](<Screenshots/kafka producer script success.png>)

***Consuming the data as an Apache Kafka consumer and pushing the data to S3 Bronze layer.***
![alt text](<Screenshots/kafka consumer written to s3.png>)

***Received SNS notification as files were uploaded to the Bronze layer.***
![alt text](<Screenshots/sns notif mail.png>)

***Moving files from "bronze_layer" to "bronze" using AWS CLI.***
![alt text](<Screenshots/moving files from silver layer to new silver using cli.png>)

***Files successfully pushed to the Silver layer after data cleaning and normalization using Apache Spark.***
![alt text](<Screenshots/files in silver.png>)

***Creating a AWS Glue crawler on the Silver layer.***
![alt text](<Screenshots/glue crawler for silver.png>)

***Crawler successfully fetched the schema and metadata into the Glue database.***
![alt text](<Screenshots/crawler success.png>)

***Dimensional data modeling (Star Schema) performed, and files written to S3.***
![alt text](<Screenshots/files in gold.png>)

***Received SNS notification for files ingested into the Gold layer.***
![alt text](<Screenshots/sns gold layer notif.png>)

***Creating an RDS Database.***
![alt text](<Screenshots/created rds database.png>)

***Creating a table and schema in the RDS database using pgAdmin 4.***
![alt text](<Screenshots/created table schema in pgadmin4.png>)

***Glue script job successfully pushed data from S3 to RDS.***
![alt text](<Screenshots/glue script run from s3 to rds.png>)

***The table in the RDS database now contains the updated data.***
![alt text](<Screenshots/table reflected in rds database.png>)

***Creating a DMS database for CDC.***
![alt text](<Screenshots/DMS DB.png>)

***Taking a snapshot of the data stored in Amazon S3.***
![alt text](<Screenshots/DMS Snapshot.png>)

![alt text](<Screenshots/DMS snapshot 2.png>)

![alt text](<Screenshots/DMS Snapshot 3.png>)

***Snapshot successfully stored in S3.***

![alt text](<Screenshots/DMS snapshot in S3.png>)

> ⚠️ **Note:** Screenshots of Silver and Gold layer transformations are not included here, as the detailed steps are available in the attached **Glue Notebooks** within the repository.

---

## 📝 Summary  

This pipeline shows how to combine **real-time streaming (local Kafka)** with AWS managed services to build a scalable **Medallion-style Data Lake** for financial transactions.  
It automates ingestion, cleansing, dimensional modeling, KPI checks, and CDC replication with minimal manual intervention.  

## Next Steps / Continuation:

This project (Project 4) continues in Project 5, where the entire pipeline will be automated using AWS Step Functions. In this automated setup, the only manual task will be uploading files to the S3 location. All other processes—including ingestion, KPI validation/reconciliation, transformations, notifications, and replication - will run automatically.