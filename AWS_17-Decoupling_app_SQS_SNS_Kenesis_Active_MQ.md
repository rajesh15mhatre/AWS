# Section 17: AWS Integration and Messeging   - Decoupling App   - SQS, SNS, Kinesis, Active MQ
Link: https://www.udemy.com/course/aws  -certified  -solutions  -architect  -associate  -saa  -c03/learn/lecture/13528448#overview

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
      - using Kinesis: real  -time streaming model
    - These services can scale independently from our application

## 182. Amazon SQS   - Standard Queue Service Overview
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
    - Best  -effort message ordering
    - Consumers delete messages after processing them
    - We can scale consumers horizontally to improve throughput of processing
  - SQS with Auto Scaling Group (ASG)
    - We can manage ASG via usign cloudWatch to check SQS queue length and raise cloudwatch alarm to scale in/out ASG  ->EC2s
  - Amazon SQS   - Security
    - **Encryption**:
      - In  -flight encryption using HTTPS API
      - At  -rest encryption using KMS keys
      - Client  -side encryption if the client wants to perform encryption/decryption itself
    - **Access Controls**: IAM policies to regulate access to the SQS API
    - **SQS Access Policies** (similar to S3 bucket policies)
      - Useful for cross  -account access to SQS queues
      - Useful for allowing other services (SNS, S3…) to write to an SQS queue

## 183. SQS   - Hands On
  - SQS   -> create a queue   -> select type std/FIFO
  - Give name
  - Provide configuration, Encryption 
  - Select default policy   - create queue
  - Goto queue   - click on send or receive messages
  - Send hello world message 
  -click on poll message  - check message in received section 
  - Delete message

## 184. SQS   - Message Visibility Timeout
  - Overview
    - After a message is polled by a consumer, it becomes **invisible** to other consumers
    - By default, the “message visibility timeout” is **30 seconds**
    - That means the message has 30 seconds to be processed
    - After the message visibility timeout is over, the message is “visible” in SQS
    - If a message is not processed within the visibility timeout, it will be processed **twice**
    - A consumer could call the **ChangeMessageVisibility** API to get more time
    - If visibility timeout is high (hours), and consumer crashes, re  -processing will take time
    - If visibility timeout is too low (seconds), we may get duplicates
  - hands on: We can change visibility timeout by going Queue   - Edit   - cofiguration   - set new time

## 185. SQS   - Long Polling
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
    - Exactly  -once send capability (by removing duplicates)
    - Messages are processed in order by the consumer

  ## 187. SQS with ASG
    - If the SQS queue load is too big, some transactions may be lost
  - Then we may go for:
    - SQS as a buffer   -  having Ec2 between SQS as Enqueue and dequeue and last to backend 
    - SQS to decouple between application tiers, where SQS is infinitely scalable and only msg gets deleted when process is complete

  ## 188. AMazon Simple Notification Service   - SNS
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
      - In  -flight encryption using HTTPS API
      - At  -rest encryption using KMS keys
      - Client  -side encryption if the client wants to perform encryption/decryption itself
    - **Access Controls:** IAM policies to regulate access to the SNS API
    - **SNS Access Policies** (similar to S3 bucket policies)
      - Useful for cross  -account access to SNS topics
      - Useful for allowing other services ( S3…) to write to an SNS topic

## 189. SNS and SQS   -Fan Out Pattern
  - SNS + SQS   -Fan Out Pattern 
    - Push once in SNS, receive in all SQS queues that are subscribers
    - Fully decoupled, no data loss
    - SQS allows for: data persistence, delayed processing and retries of work
    - Ability to add more SQS subscribers over time
    - Make sure your SQS queue **access policy** allows for SNS to write
    - Cross  -Region Delivery: works with SQS Queues in other regions
  - Application: S3 Events to multiple queues
    - For the same combination of: event type (e.g. object create) and prefix (e.g. images/) you can only have **one S3 Event rule**
    - If you want to send the same S3 event to many SQS queues, use fan  -out
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

## 190. SNS   - Hands On
  - SNS   - Create topic   - select type (fifo/std)
  - Give name   - Create
  - CReate subscription   - Email   - give email id 
    - Use mailnator to setup temp inbox with email id 
  - COnfirm subscription from email and sent test msg 
  - delete subscription and topic 

## 191. Amazon Kinesis Overview   - IMP 
  - Makes it easy to **collect, process, and analyze** streaming data in real  -time
    - Ingest real  -time data such as: Application logs, Metrics, Website clickstreams,
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
  - COnsumer   - data recives from Kinesis are called consumer which can be app, lambda, Kinesis firehoe, Kinesis data analytics
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
      - Each shard gets 1MB/s in (or 1000 records per second)   - write from source
      - Each shard gets 2MB/s out (classic or enhanced fan  -out consumer)   - read from consumer
      - You pay per shard provisioned per hour
    - On  -demand mode:
      - No need to provision or manage the capacity
      - Default capacity provisioned (4 MB/s in or 4000 records per second)
      - Scales automatically based on observed throughput peak during the last 30 days
      - Pay per stream per hour & data in/out per GB
  - Kinesis Data Streams Security
    - Control access/authorization using IAM policies
    - Encryption in flight using HTTPS endpoints
    - Encryption at rest using KMS
    - You can implement encryption/decryption of data on the client side (harder)
    - VPC Endpoints available for Kinesis to access within VPC
    - Monitor API calls using CloudTrail

## 193. Kinesis Data Stream   - Hands  -on   - No free tier
  - Search kinesis   - select option   - data stream
  - Use CLI command to store stream into kinesis and to retrieve it from kinesis commands are given in resources


## 194. Kinesis data Firehose   - overview
  - Overview 
    - Producers can be app, client, kinesis data stream, Sdk, KPL, cloud watch, AWS IoT, Kinesis Agent
    - records   - 1mBPS data can be read from producer to Kinesis firehose
    - Post data loading optionally data can be processed using Lambda 
    - Data will be written in batches to:
      - AWS destinations:
        - S3
        - Redshift   - copy through s3
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
      - 60 seconds latency minimum for nonfull batches
      - Or a minimum of 1 MB of data at a time
    - Supports many data formats, conversions, transformations, compression
    - Supports custom data transformations using AWS Lambda
    - Can send failed or all data to a backup S3 bucket
  - Kinesis Data Stream VS firehose
    - Data Stream
      - Streaming service for ingest at scale
      - Write custom code (producer / consumer)
      - Real  -time (~200 ms)
      - Manage scaling (shard splitting / merging)
      - Data storage for 1 to 365 days
      - Supports replay capability 
    - Firehose
      - Load streaming data into S3 / Redshift / OpenSearch / 3rd party / custom HTTP
      - Fully managed
      - Near real  -time (buffer time min. 60 sec)
      - Automatic scaling
      - No data storage
      - Doesn’t support replay capability

  ## 195. Kinesis Data Firehose   - Hands On
    - Goto data stram   - Delivery stream 
    - create stream   - select source kinesis stream   - 
    - select destination s3
    - give s3 sub bucket name for error output
    - create a delivery stream button click
    - Goto data stream   - use cli and using command send test data
    - test if data is available in s3 backup bucket 
    - delete stream and delivery stream

  ## 196. Kinesis Data Analytics
  just a note no video
     -  - ...It will be discussed in the analytics section of this course 😄

## 197. Data ordering for Kenisis vs SQS FIFO
  - Use the **Partition Key** to order the data in **Kinesis**
    - ex. user truck id in case N trucks are sending data. data of the same truck ID will go in the same shard
  - Kinesis will try to equally send data of trucks amongst the shards
  -  Use group ID to order data in **SQS**
    - We can have separate group id for separate consumer 
  - Kinesis vs SQS ordring
  - Let’s assume 100 trucks sending data and we have 5 kinesis shards and 1 SQS FIFO queue
    - Kinesis Data Streams   - 5 shard:
      - On average you’ll have 20 trucks per shard ( 100/5)
      - Trucks will have their data ordered within each shard
      - The maximum amount of consumers in parallel we can have is 5 as we have 5 shards
      - Can receive up to 5 MB/s of data ( 1mbps per shard)
    - SQS FIFO   - 1 queue
      - You only have one SQS FIFO queue
      - You will have 100 Group IDs ( 1 ID per truck)
      - You can have up to 100 Consumers (due to the 100 Group ID)
      - You have up to 300 messages per second (or 3000 if using batching)
## 198 SQS vs SNS vs Kinesis
  - SQS
    - Consumer “pull data”
    - Data is deleted after being consumed
    - Can have as many workers (consumers) as we want
    - No need to provision throughput
    - Ordering guarantees only on FIFO queues
    - Individual message delay capability
  - SNS
    - Push data to many subscribers
    - Up to 12,500,000 subscribers
    - Data is not persisted (lost if not delivered)
    - Pub/Sub
    - Up to 100,000 topics
    - No need to provision throughput
    - Integrates with SQS for fanout architecture pattern
    - FIFO capability for SQS FIFO
  - Kinesis
    - Standard: pull data
      - 2 MB per shard
    - Enhanced  -fan out: push data
      - 2 MB per shard per consumer
    - Possibility to replay data
    - Meant for real  -time big data, analytics and ETL
    - Ordering at the shard level
    - Data expires after X days
    - Provisioned mode or on  -demand capacity mode
 
## 199. Amazon MQ
- Overview
  - SQS, SNS are “cloud  -native” services: proprietary protocols from AWS
  - Traditional applications running from on  -premises may use open protocols such as: MQTT, AMQP, STOMP, Openwire, WSS
  - When migrating to the cloud, instead of re  -engineering the application to use SQS and SNS, we can use Amazon MQ
  - **Amazon MQ is a managed message broker service for : Rabbit MQ and Active MQ**
  - Amazon MQ doesn’t “scale” as much as SQS / SNS
  - Amazon MQ runs on servers, can run in Multi  -AZ with failover
  - Amazon MQ has both queue feature (~SQS) and topic features (~SNS)
- Amazon MQ - High Availability
  - we can use EFS for storage so in case of failure of active MQ, a standby MQ can be used by the client app with the same EFS which ensures quick failover ( HA).

## Quiz
1. You have an e-commerce website and you are preparing for Black Friday which is the biggest sale of the year. You expect that your traffic will increase by 100x. Your website already using an SQS Standard Queue, and you're running a fleet of EC2 instances in an Auto Scaling Group to consume SQS messages. What should you do to prepare your SQS Queue?
  - contact AWS support to pre-warm your SQS standard queue
  - Enable auto scaling in your SQS queue
  - Increase the capacity of the SQS queue
  - **Do nothing, SQS scales automatically**

2. You have an SQS Queue where each consumer polls 10 messages at a time and finishes processing them in 1 minute. After a while, you noticed that the same SQS messages are received by different consumers resulting in your messages being processed more than once. What should you do to resolve this issue?
  - Enable Long polling
  - Add DelaySeconds parameter to the message when being produced
  - **Increase the visibility timeout**
    - SQS Visibility Timeout is a period of time during which Amazon SQS prevents other consumers from receiving and processing the message again. In Visibility Timeout, a message is hidden only after it is consumed from the queue. Increasing the Visibility Timeout gives more time to the consumer to process the message and prevent duplicate reading of the message. (default: 30 sec., min.: 0 sec., max.: 12 hours)
  - Decrease the visibility timeout

3. Which SQS Queue type allows your message to be processed exactly once and in order?
- SQS standard queue
- SQS dead letter queue
- SQS delay queue
- **SQS FIFO queue**
  - SQS FIFO (First-In-First-Out) Queues have all the capabilities of the SQS Standard Queue, plus the following two features. First, The order in which messages are sent and received is strictly preserved and a message is delivered once and remains available until a consumer processes and deletes it. Second, duplicated messages are not introduced into the queue. 

4. You have 3 different applications that you'd like to send them the same message. All 3 applications are using SQS. What is the best approach would you choose?
- Use SQS Replication Feature
- **Use SNS+SQS Fan out Pattern**
  - This is a common pattern where only one message is sent to the SNS topic and then "fan-out" to multiple SQS queues. This approach has the following features: it's fully decoupled, no data loss, and you have the ability to add more SQS queues (more applications) over time.
- Send messages individually to 3 SQS queues

5. You have a Kinesis data stream with 6 shards provisioned. This data stream usually receiving 5 MB/s of data and sending out 8 MB/s. Occasionally, your traffic spikes up to 2x and you get a ProvisionedThroughputExceeded exception. What should you do to resolve the issue?
- **Add more shards**
    - The capacity limits of a Kinesis data stream are defined by the number of shards within the data stream. The limits can be exceeded by either data throughput or the number of reading data calls. Each shard allows for 1 MB/s incoming data and 2 MB/s outgoing data. You should increase the number of shards within your data stream to provide enough capacity.
- Enable Kinesis Replication
- Use SQS as a buffer to Kinesis

6. You have a website where you want to analyze clickstream data such as the sequence of clicks a user makes, the amount of time a user spends, and where the navigation begins and how it ends. You decided to use Amazon Kinesis, so you have configured the website to send these clickstream data all the way to a Kinesis data stream. While you checking the data sent to your Kinesis data stream, you found that the users' data is not ordered and the data for one individual user is spread across many shards. How would you fix this problem?
- There are too many shards, you should only use 1 shard
- You shouldn't use multiple consumers, only one and or should re-order data
- **For each record sent to Kinesis add a partition key that represents the identity of the user**
  - Kinesis Data Stream uses the partition key associated with each data record to determine which shard a given data record belongs to. When you use the identity of each user as the partition key, this ensures the data for each user is ordered and hence sent to the same shard.

7. You are running an application that produces a large amount of real-time data that you want to load into S3 and Redshift. Also, these data need to be transformed before being delivered to their destination. What is the best architecture would you choose?
- SQS + AWS Lambda
- SNS + HTTP Endpoint
- **Kinesis Data streams+ Kinesis Data Firehose**
  - This is a perfect combo of technology for loading data near real-time data into S3 and Redshift. Kinesis Data Firehose supports custom data transformations using AWS Lambda.

8. Which of the following is **NOT** a supported subscriber for AWS SNS?
- **Amazon Kinesis Data streams**
  - Kinesis Data Firehose is now supported, but not Kinesis Data Streams.
- Amazon SQS
- HTTP(S) Endpoint
- AWS Lambda

9. Which AWS service helps you when you want to send email notifications to your users?
- Amazon SQS with Lambda
- **SNS**
- Kinesis

10. You're running many micro-services applications on-premises and they communicate using a message broker that supports MQTT protocol. You're planning to migrate these applications to AWS without re-engineering the applications and modifying the code. Which AWS service allows you to get a managed message broker that supports the MQTT protocol?
- SQS
- SNS
- Kinesis
- **MQ**
  - Amazon MQ supports industry-standard APIs such as JMS and NMS, and protocols for messaging, including AMQP, STOMP, MQTT, and WebSocket.

11. An e-commerce company is preparing for a big marketing promotion that will bring millions of transactions. Their website is hosted on EC2 instances in an Auto Scaling Group and they are using Amazon Aurora as their database. The Aurora database has a bottleneck and a lot of transactions have been failed in the last promotion they made as they had a lot of transactions and the Aurora database wasn’t prepared to handle these too many transactions. What do you recommend to handle those transactions and prevent any failed transactions?
- **Use SQS as a buffer to write to Aurora**
- Host the website in AWS Fargate instead of EC2 instances
- Migrate Aurora to RDS for SQL Server

12. A company is using Amazon Kinesis Data Streams to ingest clickstream data and then do some analytical processes on it. There is a campaign in the next few days and the traffic is unpredictable which might grow up to 100x. What Kinesis Data Stream capacity mode do you recommend?
- Provisioned Mode
- **On-demand Mode**


