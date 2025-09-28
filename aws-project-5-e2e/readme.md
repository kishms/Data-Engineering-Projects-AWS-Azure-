# 🔷 AWS Medallion Architecture – Financial Transactions Pipeline (Project 5)  
**Automated Incremental Data Processing + KPI Validation + Step Functions Orchestration**

---

## 📖 Overview  
This project is a **continuation of Project 4**, where we built a full Medallion Architecture pipeline for **financial transactions**, including local Kafka ingestion, S3 Bronze/Silver/Gold layers, AWS Lambda for KPI checks, AWS Glue transformations, and AWS DMS CDC replication.

In **Project 5**, we automate the pipeline using **AWS Step Functions**, ensuring that **once a new file is uploaded to S3**, it flows through the **Silver and Gold layers automatically**.  

Key highlights:

- Minimal human effort: only uploading files to S3 is required.  
- Lambda functions perform **KPI validation and reconciliation** on uploaded files.  
- Step Functions orchestrate **Glue jobs** for Silver and Gold layer processing.  
- SNS notifications are triggered post-processing for job completion alerts.  
- Incremental processing ensures only **new records** (based on `dms_id`) are pushed from Bronze → Silver → Gold.

---

## 📂 Dataset  
**Financial Transactions Dataset (Kaggle)**  
- Contains both **old and new records**.  
- Primary key for incremental processing: `dms_id`.  
- Fields include:
  - `dms_id`
  - `amount`
  - `date`
  - `use_chip`
  - `zip`  

The pipeline **processes only incremental records** uploaded in fresh files. Historical data is assumed to be already present in Silver/Gold layers or stored as a DMS snapshot.

---

## 🏗️ Architecture  

**S3 Bronze → Lambda (KPI & Reconciliation) → Step Functions → Glue Silver → Glue Gold → SNS Notification**

---

## ⚙️ Services & Components  

| Layer           | Service / Tool              | Purpose                                             |
|-----------------|----------------------------|-----------------------------------------------------|
| Ingestion       | Amazon S3                  | Bronze layer for fresh file uploads                |
| Trigger / KPI   | AWS Lambda + SNS           | Validates KPI and reconciliation automatically     |
| Orchestration   | AWS Step Functions         | Automates Silver → Gold processing                 |
| Processing      | AWS Glue (PySpark)         | Data cleaning, normalization, dimensional modeling |
| Notification    | SNS                        | Alerts for successful job completion               |

---

## 🔄 Pipeline Workflow  

### 1️⃣ File Upload → S3 Bronze  
- Human uploads a **new CSV/Parquet file** containing fresh transactions.  
- **S3 Event Notification** triggers a Lambda function.

### 2️⃣ KPI Validation & Reconciliation  
- Lambda function validates basic KPIs (row count, total amount) and reconciliation checks.  
- Alerts are sent via SNS if any validation fails.

### 3️⃣ Silver Layer – Glue Transformations  
- Step Function triggers **Glue job for Silver layer**.  
- **Data cleansing and normalization** applied to incremental records.  
- Fresh incremental records are written to **S3 Silver**.  

### 4️⃣ Gold Layer – Dimensional Modeling  
- Step Function triggers **Glue job for Gold layer**.  
- Aggregations and star-schema dimensional modeling applied.  
- Incremental records pushed to **S3 Gold**.  
- **SNS notification** sent for job completion.

> ⚠️ **Note:**  
> The Glue scripts currently overwrite Silver/Gold layers in this demo, but the **intended behavior is to process only incremental data** based on the primary key `dms_id`. Historical data is assumed to be captured via **DMS snapshots** or previous pipeline runs.

---

## 📸 Screenshots  

***BUILDING THE PIPELINE***

***Creating a Lambda function triggered by S3 file upload***  
![alt text](<Screenshots/lambda function.png>)

***Lambda function attached to S3 event trigger***  
![alt text](<Screenshots/S3 event attached with Lambda.png>)

***Creating a Glue job for automated Silver and Gold layer transformations***  
![alt text](<Screenshots/Screenshot 2025-09-28 at 10.24.11 AM.png>)


***Creating an AWS Step Function to automate the process***  
![alt text](<Screenshots/Step Function Created.png>)

***AUTOMATED RESULTS***

***File uploaded to S3***
![alt text](<Screenshots/S3 file upload success.png>)

![alt text](<Screenshots/file uploaded to s3.png>)

***Lambda performed KPI and reconciliation checks***
![alt text](<Screenshots/KPI lambda.png>)

***Step Function ran successfully without human intervention***
![alt text](<Screenshots/step function success 1.png>)

![alt text](<Screenshots/success 2.png>)

***Glue job ran successfully and pushed data to the Gold layer***
![alt text](<Screenshots/Screenshot 2025-09-28 at 10.23.12 AM.png>)

***Data successfully written in the Gold layer***
![alt text](<Screenshots/output in gold.png>)

***Received SNS notification for data uploaded to the Gold layer***
![alt text](<Screenshots/gold layer sns.png>)

---

## 📝 Summary  

This project demonstrates **full automation of a Medallion Architecture pipeline** using **AWS Step Functions**, Glue, Lambda, and SNS.  

- Only **manual step**: uploading the file to S3 Bronze.  
- **Step Functions orchestrate the pipeline**, ensuring Silver and Gold transformations are applied automatically.  
- **KPI validation and reconciliation** ensure data quality.  
- **Incremental data processing** ensures that only new transactions are propagated downstream.

This completes the automated continuation of Project 4.
