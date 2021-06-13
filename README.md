
# Credit Card Transactions - Stream/Batch Processing in AWS

# Introduction & Goals
In this project, I took a credit card transaction dataset from Kaggle and I tried to built an end to end stream/batch pipeline (data collection, ETL, Reporting, etc.) in AWS using different AWS services. Below are the goals/use cases that I defined when I started with this project.

**Main Use Case (Transactional Database)**

1. *Work with Financial Transactions:*
- Storage of transactions (Credit card [transfer] details to merchants) and user access to the individuals transactions
- Alerting fraudulent transactions in the dashboard in real time / trigger email or message notifications
- Monitor real time transactions in dashboard (city wise, state wise, timewise etc., both authentic and fraudulent transactions).

2. *Technical requirement:*
- Tune transactional database tables for better read/write performance (Highly Normalized)


**Analytical Use Case (Data Warehouse)**

1. *Business Intelligence:*
- Cube models (just drag and drop dimensions across fact tables in Power BI or any other similar reporting tool to view aggregated analytics) example: Fraud transactions per city for a specific time frame, overall transaction on a specific day for a specific location etc
- Predictive Analytics on different dimensions if possible

2. *Technical Requirement:*
- Fact and Dimensional tables (Star Schema) in a data warehouse
Fact: Transaction , Dimensions: Customer, Address, Merchant, Time

# Contents

- [The Data Set](#the-data-set)
- [Used Tools](#used-tools)
  - [Connect](#connect)
  - [Buffer](#buffer)
  - [Processing](#processing)
  - [Storage](#storage)
  - [Visualization](#visualization)
- [Pipelines](#pipelines)
  - [Stream Processing](#stream-processing)
    - [Storing Data Stream](#storing-data-stream)
    - [Processing Data Stream](#processing-data-stream)
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

![alt text](https://miro.medium.com/max/3005/1*nnjxMRTXv78gbnQNOtlL9A.jpeg)


**ER Diagram for Batch Processing Pipeline (OLAP)**

![alt text](https://miro.medium.com/max/3235/1*F_U5HtcQpl7n-ySTwmFKtg.jpeg)


# Used Tools
- Explain which tools do you use and why
- How do they work (don't go too deep into details, but add links)
- Why did you choose them
- How did you set them up

## Connect
## Buffer
## Processing
## Storage
## Visualization

# Pipelines
- Explain the pipelines for processing that you are building
- Go through your development and add your source code

## Stream Processing
### Storing Data Stream
### Processing Data Stream
## Batch Processing
## Visualizations

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
