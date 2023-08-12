# Section 17: AWS Integration and Messeging - Decoupling App - SQS, SNS, Kinesis, Active MQ
Link: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528448#overview

## 181: Introduction to messeging 
- Overview
  - When we start deploying multiple applications, they will inevitably need to communicate with one another
  - There are two patterns of application communication
    - Synchronous communications (application to application)
    - Asynchronous / Event based (application to queue to application)
  - Synchronous between applications can be problematic if there are sudden spikes of traffic
  - What if you need to suddenly encode 1000 videos but usually it’s 10?
  - In that case, it’s better to decouple your applications,
    - using SQS: queue model
    - using SNS: pub/sub model
    - using Kinesis: real-time streaming model
  - These services can scale independently from our application

## 182. Amazon SQS - Standard Queue Service Overview
- Amazon SQS – Standard Queue
  - Oldest offering (over 10 years old)
  - Fully managed service, used to **decouple applications** ( Adding a middleware SQS to communicate between app and backend)
  - Attributes:
    - Unlimited throughput, unlimited number of messages in queue
    - Default retention of messages: 4 days, maximum of 14 days
    - Low latency (<10 ms on publish and receive)
    - Limitation of 256KB per message sent
  - Can have duplicate messages (at least once delivery, occasionally)
  - Can have out of order messages (best effort ordering)
- SQS – Consuming Messages
  - Consumers (running on EC2 instances, servers, or AWS Lambda)…
  - Poll SQS for messages (receive up to 10 messages at a time)
  - Process the messages (example: insert the message into an RDS database)
  - Delete the messages using the DeleteMessage API
- SQS – Multiple EC2 Instances Consumers
  - Consumers receive and process messages in parallel
  - At least once delivery
  - Best-effort message ordering
  - Consumers delete messages after processing them
  - We can scale consumers horizontally to improve throughput of processing
- SQS with Auto Scaling Group (ASG)
  - We can manage ASG via usign cloudWatch to check SQS queue length and raise cloudwatch alarm to scale in/out ASG->EC2s
- Amazon SQS - Security
  - **Encryption**:
    - In-flight encryption using HTTPS API
    - At-rest encryption using KMS keys
    - Client-side encryption if the client wants to perform encryption/decryption itself
  - **Access Controls**: IAM policies to regulate access to the SQS API
  - **SQS Access Policies** (similar to S3 bucket policies)
    - Useful for cross-account access to SQS queues
    - Useful for allowing other services (SNS, S3…) to write to an SQS queue

## 183. SQS - Hands On
- SQS -> create a queue -> select type std/FIFO
- Give name
- Provide configuration, Encryption 
- Select default policy - create queue
- Goto queue - click on send or receive messages
- Send hello world message 
-click on poll message- check message in received section 
- Delete message

## 184. SQS - Message Visibility Timeout
- Overview
  - After a message is polled by a consumer, it becomes **invisible** to other consumers
  - By default, the “message visibility timeout” is **30 seconds**
  - That means the message has 30 seconds to be processed
  - After the message visibility timeout is over, the message is “visible” in SQS
  - If a message is not processed within the visibility timeout, it will be processed **twice**
  - A consumer could call the **ChangeMessageVisibility** API to get more time
  - If visibility timeout is high (hours), and consumer crashes, re-processing will take time
  - If visibility timeout is too low (seconds), we may get duplicates
- hands on: We can change visibility timeout by going Queue - Edit - cofiguration - set new time

## 185. SQS - Long Polling
- Overview
  - When a consumer requests messages from the queue, it can optionally “wait” for messages to arrive if there are none in the queue
  - This is called Long Polling
  - **LongPolling decreases the number of API calls made to SQS while increasing the efficiency and reducing latency of your application**
  - The wait time can be between 1 sec to 20 sec (20 sec preferable)
  - Long Polling is preferable to Short Polling
  - Long polling can be enabled at the queue level or at the API level using **WaitTimeSeconds**

## 186. SQS FIFO Queue
- Overview
  - FIFO = First In First Out (ordering of messages in the queue)
  - Limited throughput: 300 msg/s without batching, 3000 msg/s with
  - Exactly-once send capability (by removing duplicates)
  - Messages are processed in order by the consumer

  ## 187. SQS with ASG
  - If the SQS queue load is too big, some transactions may be lost
- Then we may go for:
  - SQS as a buffer -  having Ec2 between SQS as Enqueue and dequeue and last to backend 
  - SQS to decouple between application tiers, where SQS is infinitely scalable and only msg gets deleted when process is complete

  ## 188. AMazon Simple Notification Service - SNS
- Overview 
  - SNS follows pub/sub pattern (publisher and subscriber)
  - The “event producer” only sends message to one SNS topic
  - As many “event receivers” (subscriptions) as we want to listen to the SNS topic notifications
  - Each subscriber to the topic will get all the messages (note: new feature to filter messages)
  - Up to 12,500,000 subscriptions per topic
  - 100,000 topics limit
  - SNS integrates with a lot of AWS services
- Amazon SNS – How to publish
  - Topic Publish (using the SDK)
    - Create a topic
    - Create a subscription (or many)
    - Publish to the topic
  - Direct Publish (for mobile apps SDK)
    - Create a platform application
    - Create a platform endpoint
    - Publish to the platform endpoint
    - Works with Google GCM, Apple APNS, Amazon ADM…
- Amazon SNS – Security
  - **Encryption:**
    - In-flight encryption using HTTPS API
    - At-rest encryption using KMS keys
    - Client-side encryption if the client wants to perform encryption/decryption itself
  - **Access Controls:** IAM policies to regulate access to the SNS API
  - **SNS Access Policies** (similar to S3 bucket policies)
    - Useful for cross-account access to SNS topics
    - Useful for allowing other services ( S3…) to write to an SNS topic

## 189. SNS and SQS -Fan Out Pattern
- SNS + SQS -Fan Out Pattern 
  - Push once in SNS, receive in all SQS queues that are subscribers
  - Fully decoupled, no data loss
  - SQS allows for: data persistence, delayed processing and retries of work
  - Ability to add more SQS subscribers over time
  - Make sure your SQS queue **access policy** allows for SNS to write
  - Cross-Region Delivery: works with SQS Queues in other regions
- Application: S3 Events to multiple queues
  - For the same combination of: event type (e.g. object create) and prefix (e.g. images/) you can only have **one S3 Event rule**
  - If you want to send the same S3 event to many SQS queues, use fan-out
- Application: SNS to Amazon S3 through Kinesis Data Firehose
  - SNS can send to Kinesis and therefore we can have the following solutions architecture
- Amazon SNS – FIFO Topic
  - FIFO = First In First Out (ordering of messages in the topic)
  - Similar features as SQS FIFO:
    - **Ordering** by Message Group ID (all messages in the same group are ordered)
    - **Deduplication** using a Deduplication ID or Content Based Deduplication
  - *Can only have SQS FIFO queues as subscribers*
  - Limited throughput (same throughput as SQS FIFO)
- SNS FIFO + SQS FIFO: Fan Out
  - In case you need fan out + ordering + deduplication
- SNS – Message Filtering
  - JSON policy used to filter messages sent to SNS topic’s subscriptions
  - If a subscription doesn’t have a filter policy, it receives every message

## 190. SNS - Hands On
- SNS - Create topic - select type (fifo/std)
- Give name - Create
- CReate subscription - Email - give email id 
  - Use mailnator to setup temp inbox with email id 
- COnfirm subscription from email and sent test msg 
- delete subscription and topic 

## 191. Amazon Kinesis Overview - IMP 
- Makes it easy to **collect, process, and analyze** streaming data in real-time
  - Ingest real-time data such as: Application logs, Metrics, Website clickstreams,
IoT telemetry data…
  - **Kinesis Data Streams**: capture, process, and store data streams
  - **Kinesis Data Firehose**: load data streams into AWS data stores
  - **Kinesis Data Analytics**: analyze data streams with SQL or Apache Flink
  - **Kinesis Video Streams**: capture, process, and store video stream


## 192. Kinesis data stream 
- it have partitions as Shrard which number needs to be decided initially say 6 shards
- Producer: Source system can be app, client, SDK, Kinesis Agent 
- producer Recods: data sent by producer \called record which consist partition key which decides in which shard data will go and actual data lited to 1MB in seize per sec.
- A shard can receive 1 MBPS or 1000 msg per sec 
- COnsumer - data recives from Kinesis are called consumer which can be app, lambda, Kinesis firehoe, Kinesis data analytics
- Kinesis data stream's record: data sent by Kinesis to consumre is a called record which consists of Partition Key , Sequence number (where records was in shard) and data blob. Speed is 2 MBPS per shard
 - Overview 
  - Retention between 1 day to 365 days
  - Ability to reprocess (replay) data
  - Once data is inserted in Kinesis, it can’t be deleted (immutability)
  - Data that shares the same partition goes to the same shard (ordering)
  - Producers: AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
  - Consumers:
    - Write your own: Kinesis Client Library (KCL), AWS SDK
    - Managed: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics
- Kinesis Data Streams – Capacity Modes
  - Provisioned mode:
    - You choose the number of shards provisioned, scale manually or using API
    - Each shard gets 1MB/s in (or 1000 records per second)
    - Each shard gets 2MB/s out (classic or enhanced fan-out consumer)
    - You pay per shard provisioned per hour
  - On-demand mode:
    - No need to provision or manage the capacity
    - Default capacity provisioned (4 MB/s in or 4000 records per second)
    - Scales automatically based on observed throughput peak during the last 30 days
    - Pay per stream per hour & data in/out per GB
- Kinesis Data Streams Security
  - Control access / authorization using IAM policies
  - Encryption in flight using HTTPS endpoints
  - Encryption at rest using KMS
  - You can implement encryption/decryption of data on client side (harder)
  - VPC Endpoints available for Kinesis to access within VPC
  - Monitor API calls using CloudTrail

## 193. Kinesis Data Stream - Hands on - No free tier
- Search kinesis - select option - data stream
- USe CLI command to store stream into kinesis and to retrive it from kenisis commands are given in resources


## 194. Kinesis data Firehose - overview
- Overview 
  - Producers can be app, client, kinesis data stream, Sdk, KPL, cloudwatch, AWS IoT, Kinesis Agent
  - records - 1mBPS data can be read from producer to Kinesis firehose
  - Post data loading optionally data can be processed using Lambda 
  - Data will be written in batches to:
    - AWS destinaitons:
      - S3
      - Redshift - copy through s3
      - AWS OpeanSearch
    - 3rd party destinations:
      - Datadog
      - splunk
      - mongoDB
    - Custom Destinations
      - HTTP endpoint: API
  - Firehose can backup all or failed data to s3 bucket
- Summary
  - Fully Managed Service, no administration, automatic scaling, serverless
    - AWS: Redshift / Amazon S3 / OpenSearch
    - 3rd party partner: Splunk / MongoDB / DataDog / NewRelic / …
    - Custom: send to any HTTP endpoint
  - Pay for data going through Firehose
  - **Near Real Time**
    - 60 seconds latency minimum for non full batches
    - Or minimum 1 MB of data at a time
  - Supports many data formats, conversions, transformations, compression
  - Supports custom data transformations using AWS Lambda
  - Can send failed or all data to a backup S3 bucket
- Kinesis Data Stream VS firehose
  - Data Stream
    - Streaming service for ingest at scale
    - Write custom code (producer / consumer)
    - Real-time (~200 ms)
    - Manage scaling (shard splitting / merging)
    - Data storage for 1 to 365 days
    - Supports replay capability 
  - Firehose
    - Load streaming data into S3 / Redshift / OpenSearch / 3rd party / custom HTTP
    - Fully managed
    - Near real-time (buffer time min. 60 sec)
    - Automatic scaling
    - No data storage
    - Doesn’t support replay capability

  ## 195. Kinesis Data Firehose - Hands On
  - Goto data stram - Delivery stream 
  - create stream - select source kinesis stream - 
  - select destination s3
  - give s3 sub bucket name for error output
  - create delivery stream button click
  - goto data stream - use cli and using command send test data
  - test if data is available in s3 backup bucket 
  - delete stream and edelivery stream

  ## 196. Kinesis Data Analytics
  just a note no video
   -- ...It will be discussed in the analytics section of this course 😄

## 197. Data Irdering for Kenisis vs SQS FIFO



