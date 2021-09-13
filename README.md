
# AWS Stream and Batch Processing Pipelines for Credit Card Transactions

# Introduction & Goals
Cloud computing is changing the way that businesses operate. As a Datawarehouse/ETL professional with more than 6+ years of experience, now I want to get into building data warehouses in the cloud. With that in mind and to understand the different cloud services, I built a data processing pipeline in AWS.
In this hobby project, I took a credit card transaction dataset from open-source platform Kaggle and tried to built an end to end stream/batch pipelines (data collection, ETL, Reporting, etc.) in AWS using different AWS services. Below are the goals/use cases that I defined when I started with this project.

Objectives of this project:

- Build and understand a data processing framework in AWS used for stream and batch data loading by companies
- Setup and understand cloud components involved in data streaming and batch processing (API gateway, kinesis, lambda functions, S3, Redshift, RDS, QuickSight, Cloud9 etc.)
- Understand how to spot failure points in an data processing pipeline and how to build systems resistant to failures and errors
- Understand how to approach or build an data processing pipeline from the ground up

**Main Use Case (Transactional Database)**

1. *Work with Financial Transactions:*
- Storage of transactions (Credit card [transfer] details to merchants) and user access to the individuals transactions
- Alerting fraudulent transactions in the dashboard in real time
- Monitor real time transactions in dashboard (city wise, state wise, timewise etc., both authentic and fraudulent transactions).

2. *Technical requirement:*
- Tune transactional database tables for better read/write performance (Highly Normalized)


**Analytical Use Case (Data Warehouse)**

1. *Business Intelligence:*
- Cube models (just drag and drop dimensions across fact tables in a reporting tool to view aggregated analytics) example: Fraud transactions per city for a specific time frame, overall transaction on a specific day for a specific location etc

2. *Technical Requirement:*
- Fact and Dimensional tables (Star Schema) in a data warehouse
Fact: Transaction , Dimensions: Customer, Address, Merchant, Time

# Contents

- [The Data Set](#the-data-set)
- [Used Tools](#used-tools)
  - [Client](#client)
  - [Connect](#connect)
  - [Buffer](#buffer)
  - [Processing](#processing)
  - [Storage](#storage)
  - [Visualization](#visualization)
- [Pipelines](#pipelines)
  - [Stream Processing](#stream-processing)
    - [Visualizations](#visualizations)
  - [Batch Processing](#batch-processing)
    - [Visualizations](#visualizations)
- [Conclusion](#conclusion)
- [Follow Me On](#follow-me-on)
- [Appendix](#appendix)


# The Data Set
The dataset I used in this project is taken from Kaggle and it is called "Credit Card Transactions Fraud Detection Dataset". This is a simulated credit card transaction dataset containing legitimate and fraud transactions. It covers credit cards of 1000 customers doing transactions with a pool of 800 merchants. The dataset contains details about the customers, transactions, merchants and also a flag that states if the transaction is legit or fraud.

https://www.kaggle.com/kartik2112/fraud-detection

**ER Diagram for Stream Processing Pipeline (OLTP)**

![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/4f4045a185381399500ad353553da4f9044d38b4/images/oltp.jpeg)


**ER Diagram for Batch Processing Pipeline (OLAP)**

![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/4f4045a185381399500ad353553da4f9044d38b4/images/olap.jpg)


# Used Tools
![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/e5b8b146347639d3b349aa1a4fcc1c1a276b1d15/images/used_tools.PNG)

## Client
Here the source data for the stream processing pipeline is loacted in the local PC in .csv format. The .csv data will be read by the local python script to post data to the AWS API endpoint.

## Connect
In the scenario of stream processing, a data pipeline will pull the data from an API and send data to the buffer. AWS API Gateway POST method is used to pull data from the client. Every time when the data will reach to the API endpoint, it will trigger the lambda function and send data to AWS Kinesis.

## Buffer
I used Kinesis in stream processing to queue the data. The data will lineup in Kinesis every time the API endpoint trigger the lambda function in the AWS.

## Processing
Lambda functions: Used in the stream processing pipeline in different stages. Here I used two lambda functions. One to send data from API to Kinesis and the other to send data from Kinesis to Serverless Aurora RDS.

Cloud9: AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets us write, run, and debug our code with just a browser. This is just an EC2 instance. I used this service to bulk import data from S3 .csv file into Serverless Aurora RDS.

AWS Glue Crawlers: Inorder to query the data directly from S3 using Athena and Redshift Spectrum, glue crawlers are used which inturn generates glue data catalog.

Athena & Redshift Spectrum: Utilized to query data directly from S3.

EC2 Instances: created a couple of EC2 instances for this project. One is to host Grafana and the other one for Cloud9.

## Storage
S3: Amazon Simple Storage Service is a service that acts as a data lake in this project. Source credit card transactions are hosted here for batch/bulk load. After data processing at different stages, invalid data are also stored here.

Serverless Aurora RDS: This is an OLTP database in this project. This is an relational database that contains highly normalized tables for the credit card transactions.

Redshift: Datawareouse or OLAP database. A star schema has been built for this project on this relational database.

## Visualization
Grafana: Dasboards are built to visualize the data from the Serverless Aurora RDS (OLTP).

Quicksight: Dasboards are built to visualize the data from the Redshift data warehouse and S3.

# Pipelines
## Stream Processing
![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/main/images/sp.PNG)

Credit Card Transaction Data: As mentioned already this is the kaggle dataset. I also inserted some invalid rows in the dataset (for example the credit card number or transaction numbers are null. But those details are mandatory for every valid transaction). This dataset is stored on the local PC.

Local Python Script: A [python script](https://github.com/arockianirmal26/MediumBlogPosts/blob/main/AWS_Stream_Processing_Pipeline/put_streaming_data_to_API.py) has been created locally to send the transactional data(converted to JSON format) to AWS API Gateway as put request.

AWS API Gateway: This API Gateway acts as a bridge between the local csv data and kinesis data streams.

Lambda Function — Write to Kinesis Data Streams: Once a transaction data reaches the API gateway, this [lambda function](https://github.com/arockianirmal26/MediumBlogPosts/blob/main/AWS_Stream_Processing_Pipeline/lambda_write_to_kinesis.py) will be triggered. The JSON format data is now validated here against the JSON schema defined in the lambda function. If the validation is successful, the transaction data will be sent to the respective kinesis data stream. If not the transaction data will be diverted to the S3 bucket which contains invalid data. A S3 bucket has been created to store all the invalid transactions. Each transaction will be stored as a separate file. The name of the file will be the current timestamp.

AWS Kinesis Data Stream: A basic kinesis data stream has been created with the number of open shards = 1 and with the data retention period of one day. Once the event reaches kinesis the write to aurora database function will be triggered.  

Lambda Function — Write to RDS Aurora Serverless: This [lambda function](https://github.com/arockianirmal26/MediumBlogPosts/blob/main/AWS_Stream_Processing_Pipeline/lambda_write_to_Aurora.py) will now take an event received from kinesis data streams and insert data to different OLTP tables in the aurora serverless database. There are many tables in the OLTP database. Hence only if all the inserts/updates on the tables for a particular event succeeds, then the transaction gets committed. If not, the transaction will be rolled back. Therefore all the SQL DML statements are enclosed within the transaction as in the code below.

Serverless Aurora RDS: A serverless aurora RDS has been created to store the transactional data. Attached are the [DDL statements](https://github.com/arockianirmal26/MediumBlogPosts/blob/main/AWS_Stream_Processing_Pipeline/OLTP_Tables.txt) of the OLTP database tables.

Once the local python script has been started, the pipelines ends after writing the transaction data to OLTP database to the respective tables. The cloud watch logs can be used to monitor the lambda function executions.

BULK IMPORT TO Serverless Aurora RDS: I have also created a Cloud9 EC2 instance to load the data in bulk from S3 to Serverless Aurora RDS. Here is the [python script](https://github.com/arockianirmal26/MediumBlogPosts/blob/main/AWS_Bulk_Import_S3_to_RDS_using_Cloud9/s3_cloud9_aurora.py) that is being used for this purpose.

The step by step process of the above tasks are documented as medium blog posts as below.
[Building a Stream Processing Pipeline in AWS](https://arockianirmal26.medium.com/building-a-stream-processing-pipeline-in-aws-3224764a6d2b)
[Bulk Import from AWS S3 Bucket into RDS Aurora Serverless using AWS Cloud9](https://arockianirmal26.medium.com/bulk-import-from-aws-s3-bucket-into-rds-aurora-serverless-using-aws-cloud9-10a793850765)

### Visualizations
An EC2 instance has been created and grafana has been installed on the same. This EC2 instance has been created in the same VPC as the RDS. Below is a sample dashboard that has been created. On entering the credit card number and the date of birth, the details of the credit card holder and the recent transactions can be visualized. 

![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/66c9e2426adfe128855c23f69aca09b314e6f80d/images/grafana_oltp_dashboard.png)

The step by step process of the above task is documented as medium blog post as below.
[Building a Grafana Dashboard in AWS on Serverless Aurora RDS](https://arockianirmal26.medium.com/building-a-grafana-dashboard-in-aws-on-serverless-aurora-rds-23d66a87c952)

## Batch Processing
![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/main/images/bp.PNG)

S3 Data Lake: Here are the credit card transactions data are dumped in the .csv fomat. 

Amazon Redshift: Redshift is Amazon's analytics database, and is designed to crunch large amounts of data as a data warehouse. A redshift cluster has been created for this project as a OLAP Database. Once the database has been created, a [staging table](https://github.com/arockianirmal26/MediumBlogPosts/blob/main/Populating%20Amazon%20Redshift%20DWH%20from%20S3%20%26%20QuickSight%20Reporting/transaction_staging_ddl.sql) has been created. Then the redshift copy command has been used to copy the .csv data from S3 to the created table. Then the star schema [tables](https://github.com/arockianirmal26/MediumBlogPosts/blob/main/Populating%20Amazon%20Redshift%20DWH%20from%20S3%20%26%20QuickSight%20Reporting/dwh_star_schema_dddl.sql) has been created in the data warehouse and loaded by the data warehouse [procedure](https://github.com/arockianirmal26/MediumBlogPosts/blob/main/Populating%20Amazon%20Redshift%20DWH%20from%20S3%20%26%20QuickSight%20Reporting/fact_dimensions_loading.sql). In real life only the warm data(data that we need often) are usually loaded to the database to reduce costs. The cold data (data that we do not need much often) can be kept in the datalake.   

Athena & Redshift Spectrum: Using these two services I could able to query the data from the S3 data lake, similar to querying the database.

### Visualizations
Once the batch processing has been completed, I could able to build dashboards on the Datawarehouse using Amazon Quicksight. Below is a sample dasboard.

![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/a30ba02f5a9d48deca19f6ee15f1c53a1d7b436a/images/redshift_dash.png)

Also I could able to built dashboards on the data from S3 data lake using Athena and Redshift Spectrum in Quicksight.

Dashboard using Athena as data source

![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/a30ba02f5a9d48deca19f6ee15f1c53a1d7b436a/images/athena_dash.png)

Dashboard using RedshiftSpectrum as data source

![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/a30ba02f5a9d48deca19f6ee15f1c53a1d7b436a/images/spectrum_dash.png)

The step by step process of the above tasks (Batch Processing) are documented as medium blog posts as below.
[Populating Amazon Redshift DWH from S3 & QuickSight Reporting](https://arockianirmal26.medium.com/populating-amazon-redshift-dwh-from-s3-quicksight-reporting-f6194dce2446)
[Amazon QuickSight Dashboard for S3 CSV Data Using Amazon Athena / Glue Crawler](https://arockianirmal26.medium.com/amazon-quicksight-dashboard-for-s3-csv-data-using-amazon-athena-6a38bda2d0f8)
[Amazon QuickSight Dashboard for S3 CSV Data using Redshift Spectrum / Glue Crawler](https://arockianirmal26.medium.com/amazon-quicksight-dashboard-for-s3-csv-data-using-redshift-spectrum-glue-crawler-5173a69a6b8d)


# Conclusion
This project turned out pretty well than I expected. I understood the basics of the AWS Infrastructure and its services like S3, RDS, Kinesis, API Gateway, Lambda functions, Redshift, EC2, Quicksight, Athena etc., very well. I can now confidently work on AWS data projects.

# Follow Me On
LinkedIn: https://www.linkedin.com/in/arockianirmal/
Medium: https://arockianirmal26.medium.com/

# Appendix

[Markdown Cheat Sheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
