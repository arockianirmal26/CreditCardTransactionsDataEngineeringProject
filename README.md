
# Stream and Batch Processing of Credit Card Transactions in AWS

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
    - [Storing Data Stream](#storing-data-stream)
    - [Processing Data Stream](#processing-data-stream)
    - [Visualizations](#visualizations)
  - [Batch Processing](#batch-processing)
    - [Visualizations](#visualizations)
- [Demo](#demo)
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
- Explain the pipelines for processing that you are building
- Go through your development and add your source code

## Stream Processing
![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/main/images/sp.PNG)

### Visualizations

## Batch Processing
![alt text](https://github.com/arockianirmal26/CreditCardTransactionsDataEngineeringProject/blob/main/images/bp.PNG)

### Visualizations

# Demo
- You could add a demo video here
- Or link to your presentation video of the project

# Conclusion
Write a comprehensive conclusion.
- How did this project turn out
- What major things have you learned
- What were the biggest challenges

# Follow Me On
LinkedIn: https://www.linkedin.com/in/arockianirmal/
Medium: https://arockianirmal26.medium.com/

# Appendix

[Markdown Cheat Sheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
