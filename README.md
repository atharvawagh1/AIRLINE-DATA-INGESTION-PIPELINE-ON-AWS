# AIRLINE-DATA-INGESTION-PIPELINE-ON-AWS
#https://www.youtube.com/watch?v=35Du026h-KY



# AWS Serverless Data Pipeline for Airline Analytics

![Pipeline Hero](https://images.unsplash.com/photo-1508614999368-9260051291ea?ixlib=rb-4.0.3&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=1200&h=360&fit=crop)

_A fast, scalable serverless ETL pipeline for processing daily airline flight data on AWS, with instant data ingestion and analytics._  

![AWS Lambda](https://img.shields.io/badge/AWS-Lambda-FF9900?logo=aws-lambda&logoColor=white)
![AWS Glue](https://img.shields.io/badge/AWS-Glue-FF9900?logo=aws&logoColor=white)
![AWS Step Functions](https://img.shields.io/badge/AWS-Step%20Functions-FF9900?logo=aws&logoColor=white)
![Amazon S3](https://img.shields.io/badge/Amazon-S3-569A31?logo=amazons3&logoColor=white)
![Amazon Redshift](https://img.shields.io/badge/AWS-Redshift-FF9900?logo=amazonaws&logoColor=white)
![Amazon SNS](https://img.shields.io/badge/Amazon-SNS-FF9900?logo=amazonaws&logoColor=white)

---

## 🚀 Overview

This project demonstrates an **event-driven, fully serverless data pipeline** for airline analytics built on AWS. Flight data files are ingested into Amazon S3 and immediately trigger an AWS Step Functions workflow (via Amazon EventBridge) to process and load the data.  

Key AWS services (S3, Glue, Step Functions, Redshift, SNS) are composed into a modular, no-ops pipeline that scales automatically to large datasets.  

---

## 🏗️ Architecture

![Architecture Diagram](https://miro.medium.com/v2/resize:fit:1200/1*HhUw6l0eQfJwA1bZrhQwOw.png)

- **Amazon S3** → landing zone for raw flight and airport CSVs  
- **Amazon EventBridge** → detects new uploads and triggers Step Functions  
- **AWS Step Functions** → orchestrates Glue Crawler → Glue ETL Job → Redshift Load  
- **AWS Glue** → schema discovery + ETL (PySpark transformations & enrichment)  
- **Amazon Redshift** → analytics-ready fact and dimension tables  
- **Amazon SNS** → success/failure notifications to stakeholders  

---

## ✨ Core Features

- **Event-driven ingestion** with S3 + EventBridge  
- **Automated schema discovery** using Glue Crawlers  
- **Scalable ETL** with Glue PySpark jobs for cleaning, joining, and enrichment  
- **Analytics-ready Redshift warehouse** with star schema design  
- **Reliability & visibility** via Step Functions error handling and SNS alerts  

---

## ⚙️ Setup Instructions

### Prerequisites
- AWS Account with IAM access  
- S3 bucket for raw data  
- AWS Glue Data Catalog + Crawler + ETL Job  
- Amazon Redshift cluster (or serverless endpoint)  
- AWS Step Functions workflow + EventBridge rule  
- SNS topic for notifications  

### Steps
1. Create an **S3 bucket** and upload sample `flights.csv` and `airports.csv`.  
2. Configure **Glue Crawler** to catalog S3 data.  
3. Deploy **Glue ETL Job** (`glue_job.py`) to transform and enrich data.  
4. Create **Redshift tables** using the SQL in `redshift_create_table_commands.txt`.  
5. Deploy **Step Function state machine** (`step_function_code.json`).  
6. Configure **EventBridge rule** (`event_bridge_rule.json`) to trigger Step Functions on S3 events.  
7. Subscribe stakeholders to the **SNS topic** for pipeline alerts.  
8. Upload a new flight CSV to S3 → watch the pipeline execute end-to-end.  

---

## ▶️ Demo Flow

![Pipeline Flow](https://lucid.app/publicSegments/view/6a0c4f9e-5ad0-40b4-8db5-2954c0b53f3f/image.png)

1. CSV uploaded to S3  
2. EventBridge triggers Step Functions  
3. Step Functions runs Glue Crawler → Glue Job  
4. Data loaded into Redshift  
5. SNS notifies success/failure  

---

## 📊 Example Redshift Queries

```sql
-- Average departure delay by carrier
SELECT carrier, AVG(dep_delay) AS avg_dep_delay
FROM airlines.daily_flights_fact
GROUP BY carrier
ORDER BY avg_dep_delay DESC
LIMIT 10;

-- Top origin cities with most delays
SELECT dep_city, COUNT(*) AS delayed_flights
FROM airlines.daily_flights_fact
WHERE dep_delay > 60
GROUP BY dep_city
ORDER BY delayed_flights DESC
LIMIT 10;
