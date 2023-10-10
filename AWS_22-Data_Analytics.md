# Section: Data & Analytics

## 242. Athena
## 243. Athena Handson
## 244. Redshift
## 245. OpenSearch (ex ElasticSearch)
- OpenSearch use case/ common patterns:
  - DynamoDB
    - load all data into the DynamoDB stream from DynamoDB
    - Then lambda function will load product data with ID in OpenSearch
    - Then on a need basis an API service on EC2 can retrieve the product ID searching by the product name in OpenSearch and then API will fetch relevant data from DynamoDB using the product ID
- CloudWatch Logs
  - CloudWatch Logs will send data to Subscription Filter
  - Then Either Lamdba (real-time) or Kinesis Data Firehose (near real-time) will load data into Amazon OpenSearch
- KDS & KDF
  - KDS will pass data to KDF
  - Optionally we can have Lambda function to  transform data
  - finally load it into OpenSearch
- KDS & Lambda
  - KDS will trigger the lambda function to load data into OpenSearch
 
## 246. Amazon EMR ( Elastic Map Reduce)
## 247. Amazon QuickSight
## 248. Amazon Glue
## 249. Lake Formation
- Solve access problems by providing centralized access control of all the data sources and also providing row and column level access feature
## 250. Kinesis Data Analytics
- We can perform Analytics using SQL (legacy Feature) or Apache Flink on KDA. Flink is way more powerful than SQL
## 251. Kinesis Data Analytics - Hands on
## 252. MSK - Managed Streaming FOr Apache Kafka
## 253. Big Data Ingestion Pipeline

## Quiz
1. You would like to have a database that is efficient at performing analytical queries on large sets of columnar data. You would like to connect to this Data Warehouse using a reporting and dashboard tool such as Amazon QuickSight. Which AWS technology do you recommend?
- RDS
- S3
- **Redshift**
- Neptune

2. You have a lot of log files stored in an S3 bucket that you want to perform a quick analysis, if possible Serverless, to filter the logs and find users that attempted to make an unauthorized action. Which AWS service allows you to do so?
- DynamoDB
- Redshift
- S3 Glacier
- **Athena**

3. As a Solutions Architect, you have been instructed you to prepare a disaster recovery plan for a Redshift cluster. What should you do?
- Enable Multi-AZ
- **Enable Automated Snapshots, then configure your Redshift cluster to automatically copy snapshots to another AWS region**
- Take a snapshot then restore to Redshift Global cluster

4. Which feature in Redshift forces all **COPY** and **UNLOAD** traffic moving between your cluster and data repositories through your VPCs?
- **Enhanced VPC routing**
- Improved VPC routing
- Redshift Spectrum

5. You are running a gaming website that is using DynamoDB as its data store. Users have been asking for a search feature to find other gamers by name, with partial matches if possible. Which AWS technology do you recommend to implement this feature?
- DynamoDB
- Redshift
- **OpenSearch Service**
- Neptune

6. An AWS service allows you to create, run, and monitor ETL (extract, transform, and load) jobs in a few clicks.
- **Glue**
- Redshift
- RDS
- DynamoDB
  
7. A company is using AWS to host its public websites and internal applications. Those different websites and applications generate a lot of logs and traces. There is a requirement to centrally store those logs and efficiently search and analyze those logs in real-time for detection of any errors and if there is a threat. Which AWS service can help them efficiently store and analyze logs?
- S3
- **OpenSearch**
- ElastiCache
- QLDB

8. ……………………….. makes it easy and cost-effective for data engineers and analysts to run applications built using open source big data frameworks such as Apache Spark, Hive, or Presto without having to operate or manage clusters.
- Lambda
- **EMR**
- Athena
- OpenSearch

9. An e-commerce company has all its historical data such as orders, customers, revenues, and sales for the previous years hosted on a Redshift cluster. There is a requirement to generate some dashboards and reports indicating the revenues from the previous years and the total sales, so it will be easy to define the requirements for the next year. The DevOps team is assigned to find an AWS service that can help define those dashboards and have native integration with Redshift. Which AWS service is best suited?
- OpenSearch
- Athena
- **QuickSight**
- EMR

10. Which AWS Glue feature allows you to save and track the data that has already been processed during a previous run of a Glue ETL job?
- Glue Job Bookmark
- Glue Elastic Views
- Clue Streaming ETL
- Glue DataBrew
  
11. You are a DevOps engineer in a machine learning company which 3 TB of JSON files stored in an S3 bucket. There’s a requirement to do some analytics on those files using Amazon Athena and you have been tasked to find a way to convert those files’ format from JSON to Apache Parquet. Which AWS service is best suited?
- S3 Object Versioning
- Kinesis Data Streams
- MSK
- **Glue**

12. You have an on-premises application that is used together with an on-premises Apache Kafka to receive a stream of clickstream events from multiple websites. You have been tasked to migrate this application as soon as possible without any code changes. You decided to host the application on an EC2 instance. What is the best option you recommend to migrate Apache Kafka?
- Kinesis Data Streams
- Glue
- **MSK**
- Kinesis Data Analytics

13. You have data stored in RDS, S3 buckets and you are using AWS Lake Formation as a data lake to collect, move and catalog data so you can do some analytics. You have a lot of big data and ML engineers in the company and you want to control access to part of the data as it might contain sensitive information. What can you use?
- **Lake Formation Fine-grained Access Control**
- Amazon Cognito
- AWS Shield
- S3 Object Lock

14. Which AWS service is most appropriate when you want to perform real-time analytics on streams of data?
- SQS
- SNS
- **Kinesis Data Analytics**
  - Use Kinesis Data Analytics with Kinesis Data Streams as the underlying source of data.
- Kinesis Data Firehose
 




