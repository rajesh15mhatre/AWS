# Section 13: Advance Amazon S3
Link: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528352?start=0#overview

## 140: S3 lifecycle Rules (with s3 Analytics)
- S3 – Moving between storage classes
  - You can transition objects between storage classes
  - For infrequently accessed object, move them to **STANDARD IA**
  - For archive objects you don’t need in real-time, **GLACIER** or **Glacier DEEP ARCHIVE**
  - Moving objects can be automated using a **lifecycle** configuration
- Lifecycle rules
  - **Transition actions**: It defines when objects are transitioned to another storage
class.
    - Move objects to Standard IA class 60 days after creation
    - Move to Glacier for archiving after 6 months
  - **Expiration actions**: configure objects to expire (delete) after some time
    - Access log files can be set to delete after a 365 days
    - **Can be used to delete old versions of files (if versioning is enabled)**
    - Can be used to delete incomplete multi-part uploads
  - Rules can be created for a certain prefix (ex - s3://mybucket/mp3/*)
  - Rules can be created for certain objects tags (ex - Department: Finance)
 
- Lifecycle Rules – Scenario 1
  - Your application on EC2 creates images thumbnails after profile photos are uploaded to Amazon S3. These thumbnails can be easily recreated, and only need to be kept for 45 days. The source images should be able to be immediately retrieved for these 45 days, and afterwards, the user can wait up to 6 hours. How would you design this?
    - S3 source images can be on STANDARD, with a lifecycle configuration to transition them to GLACIER after 45 days.
    - S3 thumbnails can be on ONEZONE_IA, with a lifecycle configuration to expire them (delete them) after 45 days.
- Lifecycle Rules – Scenario 2
  - A rule in your company states that you should be able to recover your deleted S3 objects immediately for 15 days, although this may happen rarely. After this time, and for up to 365 days, deleted objects should be recoverable within 48 hours.
    - You need to enable S3 versioning in order to have object versions, so that “deleted objects” are in fact hidden by a “delete marker” and can be recovered
    - You can transition these “noncurrent versions” of the object to S3_IA
    - You can transition afterwards these “noncurrent versions” to DEEP_ARCHIVE
   
- Storage Class Analysis
  - Help you determine when to transition objects
to right storage class
• Recommendation for **Standard** and **Standard IA** Does not work for ONEZONE_IA or GLACIER
• Report is updated daily
• Takes about 24h to 48h hours to first start
• Good first step to put together Lifecycle Rules (or improve them)!

# 141. S3 Lifecycle Rules - Hands On
- S3 - Bucket - Lifecycle configuration - Create
- Give name - all bucket
- Choose diffent rule by selcting multi select checkbox option
  - Move currrent version objects between storage classes
    - different input option get enabled to enter the timeframe as per selection
    - Create rule

## 142. S3 Request Pays
- In general, bucket owners pay for all Amazon S3 storage and data transfer costs associated with their bucket
• With Requester Pays buckets feature, the requester instead of the bucket owner pays the cost of the request and the data download from the bucket
• Helpful when you want to share large datasets with other accounts
• The requester must be authenticated in AWS (cannot be anonymous)

## 143. S3 Event Notificaton
- Notificaiton for : 
  - S3:ObjectCreated, S3:ObjectRemoved,
S3:ObjectRestore, S3:Replication…
  - Object name filtering possible (*.jpg)
  - Use case: generate thumbnails of images uploaded to S3
 - Can create as many “S3 events” as desired
 - S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer
• If two writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent
• If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket.
- We can sent notification to SNS, SQS or Lambda from S3 for some event to happend for that we need to enable Access policy 
- Amazon EventBridge captures all the events and we can perform
  - advance filtering with JSON RULE
  - Miltiple Destinaiton - step fucntion Kenesis Stream/ Firehose
  - Event Bridge Capabilities - Archive, Replay Events, Reliable delivery

## 144. S3 Event Notificaton - Hand on
- S3 bucket - create bucket
- properties - event notifications
- Turn on EventBridge notification (to captual all logs
- Alternatively, in simler way we can create notification
- first create new SQS service
  - edit access policy
  -  policy generator
    - effect - allow
    -  Principal - *
    -  action send mesage
    -  ARN - provide SQS name from Json
    -  Save
- give name - select event type - object create
- Select SQS queue name
- Create
- Test SQS one test message must be received
- Also test by uploading file to s3 bucket


## 145. S3 Performance
- Baseline performance
  - Amazon S3 automatically scales to high request rates, latency 100-200 ms
- Your application can achieve at least **3,500 PUT/COPY/POST/DELETE and 5,500 GET/HEAD requests per second per prefix in a bucket**.
• There are no limits to the number of prefixes in a bucket.
• Example (object path => prefix):
• bucket/folder1/sub1/file => /folder1/sub1/
• bucket/folder1/sub2/file => /folder1/sub2/
• bucket/1/file => /1/
• bucket/2/file => /2/
• If you spread reads across all four prefixes evenly, you can achieve 22,000 requests per second for GET and HEAD
Multi-Part upload:
• recommended for files > 100MB,
must use for files > 5GB
• Can help parallelize uploads (speed
up transfers)
- KMS Limitation
  - If you use SSE-KMS, you may be impacted by the KMS limits
  - When you upload, it calls the **GenerateDataKey** KMS API
  - When you download, it calls the Decrypt KMS API
  - Count towards the KMS quota per second (5500, 10000, 30000 req/s based on region)
  - You can request a quota increase using the Service Quotas Console
- Performance
  - Multi-Part upload:
    - recommended for files > 100MB, must use for files > 5GB
    - Can help parallelize uploads (speed up transfers)
  - S3 Transfer Acceleration
    - Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in the target region. As it uses more private network between Edge and S3 bucket.
    - Compatible with multi-part upload
- Performance – S3 Byte-Range Fetches
  - speed up download 
    - Parallelize GETs by requesting specific byte ranges
    - Better resilience in case of failures
    - Can be used to retrieve only partial data (for example the head of a file)


## 146. S3 Selct & Glacier Select
- Retrieve less data using SQL by performing server side filtering
- Can filter by rows & columns (simple SQL statements)
- Less network transfer, less CPU cost client-side as we filter data as server side (s3) and then retrive.

## 147. Batch Operations 
- Perform bulk operations on existing s3 objects with a single request, ex
  - modify object metadata and properties
  - copy objects between s3 buckets
  - encrypt un-encrypt objects
  - modify ACLs tags
  - Restore objects from s3 glacier
  - invoke Lambda function to perform custom action on each object
- A job consist of a list of objects, the action to perform, and optional parameters
- S3 Batch Operation manages retries tracks progess, sends completion notification, generate reports...
- **You can use S3 inventory to get object list and use s3 select to filter your objects**

## Quiz
1. How can you be notified when there's an object uploaded to your S3 bucket?
- S3 select
- S3 Access Logs
- **S3 event Notification**
- S3 Analystics

2.You have an S3 bucket that has S3 Versioning enabled. This S3 bucket has a lot of objects, and you would like to remove old object versions to reduce costs. What's the best approach to automate the deletion of these old object versions?
  - Use S3 Lifecycle Rules - Transistion Action
  - **use S3 Lifecycle Rules - Expiration Action**
  - S3 Access Logs

3. How can you automate the transition of S3 objects between their different tiers?
  - AWS lambda
  - CloudWatch Events
  - **S3 Lifecycle Rules**

4. While you're uploading large files to an S3 bucket using Multi-part Upload, there are a lot of unfinished parts stored in the S3 bucket due to network issues. You are not using these unfinished parts and they cost you money. What is the best approach to remove these unfinished parts?
    - Use AWS Lambda to loop on each old/unfinished part and delete them
    - Request AWS Support to help you delete old/unfinished parts
    - **Use an s3 Lodecycle Policy to automate old/unfinished parts deletion** 

5. You are looking to get recommendations for S3 Lifecycle Rules. How can you analyze the optimal number of days to move objects between different storage tiers?
  - S3 Inventory
  - **S3 Analytics**
  - S3 lifeCycle Rules Advisor

6. You are looking to build an index of your files in S3, using Amazon RDS PostgreSQL. To build this index, it is necessary to read the first 250 bytes of each object in S3, which contains some metadata about the content of the file itself. There are over 100,000 files in your S3 bucket, amounting to 50 TB of data. How can you build this index efficiently?
  - Use RDS import feature to load data from s3 to PostgreSQL and run a SQL query to build the index
  - Create an application that will traverse the S3 bucker read all the files one by one, extract the first 250 bytes and store the information in RDS]
  - **Create an application that will traverse the s3 bucket, issue a byte range fetch for the first 250 byte and store that information is RDS**
  - Create an app that will traverse the s3 bucket, use s3 select to get the first 250 bytes and store that information in RDS

7. You have a large dataset stored on-premises that you want to upload to the S3 bucket. The dataset is divided into 10 GB files. You have good bandwidth but your Internet connection isn't stable. What is the best way to upload this dataset to S3 and ensure that the process is fast and avoid any problems with the Internet connection?
  - Use mult-part Upload only
  - Use s3 Select and Use s3 transfer acceleration
  - **Use s3 Multi-part upload and s3 Transfer Acceleration**

8. You would like to retrieve a subset of your dataset stored in S3 with the .csv format. You would like to retrieve a month of data and only 3 columns out of 10, to minimize compute and network costs. What should you use?
 - S3 Analytics
 - S3 Access Logs
 - **S3 select**
 - S3 inventory

9. A company is preparing for compliance and regulatory review on its infrastructure on AWS. Currently, they have their files stored on S3 buckets that are not encrypted, which must be encrypted as required for compliance and regulatory review. Which S3 feature allows them to encrypt all files in their S3 buckets in the most efficient and cost-effective way?
  - S3 Access Points
  - S3 Cross-region Replication
  - **S3 batch Operation**
  - S3 Lifecycle Rules















