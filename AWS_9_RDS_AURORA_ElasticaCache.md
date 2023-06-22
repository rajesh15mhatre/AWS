# Section 9 AWS Fundametals
## 87. Amazon RDS overview
- RDS stands for Relational Database Service
- It’s a managed DB service for DB use SQL as a query language.
- It allows you to create databases in the cloud that are managed by AWS
  - Postgres
  - MySQL
  - MariaDB
  - Oracle
  - Microsoft SQL Server
  - Aurora (AWS Proprietary database)
- Advantages of RDS over deploying DB on EC2
  - RDS is a managed service:
    - Automated provisioning, OS patching
    - Continuous backups and restore to specific timestamp (Point in Time Restore)!
    - Monitoring dashboards
    - Read replicas for improved read performance
    - Multi AZ setup for DR (Disaster Recovery)
    - Maintenance windows for upgrades
    - Scaling capability (vertical and horizontal)
    - Storage backed by EBS (gp2 or io1)
  - BUT you can’t SSH into your instances 
- RDS Backups
  - Backups are automatically enabled in RDS
  - Automated backups:
    - Daily full backup of the database (during the maintenance window)
    - Transaction logs are backed-up by RDS every 5 minutes
    - => ability to restore to any point in time (from oldest backup to 5 minutes ago)
    - 7 days retention (can be increased to 35 days)
  - DB Snapshots:
    - Manually triggered by the user
    - Retention of backup for as long as you want
- RDS  - Storage Auto Scaling 
  - Helps you increase storage on your RDS DB instance dynamically
  - When RDS detects you are running out of free database storage, it scales automatically
  - Avoid manually scalling your database storage
  - You have to set **Maximum Storage Threshold** ( maximum limit for DB storage)
  - Automatically modify storage if:
    - Free storage is less than 10% of allocated storage
    - Low-storage lasts at least 5 mins
    - 6 hours have passed since last modification
  - Useful for application with unpredictable workloads
  - Supports all RDS databases engines (MariaDB, MySQL, PostgreSQL, SQL Server, Oracle)

##  88 . RDS Read replicas and Multi AZs (IMP)
- RDS Read Replicas for read scalability
  - Up to 5 Read Replicas
  - Within AZ, Cross AZ
or Cross Region
  - Replication is **ASYNC**,
so reads are eventually
consistent
  - Replicas can be
promoted to their
own DB
  - Applications must
update the connection
string to leverage read
replicas
- RDS Read Replicas – Use Cases
  - You have a production database
that is taking on normal load
  - You want to run a reporting
application to run some analytics
  - You create a Read Replica to run
the new workload there
  - The production application is
unaffected
  - Read replicas are used for SELECT
(=read) only kind of statements
(not INSERT, UPDATE, DELETE)
- RDS Read Replicas – Network Cost
  - In AWS there’s a network cost when data goes from one AZ to another
  - **For RDS Read Replicas within the same region, you don’t pay that fee but its chargable when data replicated in AZ of a differnt region**
- RDA Multi AZ (Disaster recovery)
  - SYNC replication
  - One DNS name – automatic app failover to standby
  - Increase availability
  - Failover in case of loss of AZ, loss of network, instance or storage failure
  - No manual intervention in apps
  - Not used for scaling
  - Multi-AZ replication is free
  - Note:The Read Replicas be setup as Multi AZ for Disaster Recovery (DR)
- RDS - from single AZ to Multi AZ
  - Zero downtime operation (no need to stop the DB)
  - Just click on “modify” for the database
  - The following happens internally:
    - A snapshot is taken
    - A new DB is restored from the snapshot in a new AZ
    -  Synchronization is established between the two databases

## 89. Amzaon RDS Hands on
- goto RDS - create DB - select engine 
- selct muysql ansd version
- selct template - production
- under availability and durability
  -  Single DB instance/ Multi/ Multi cluster
- Select db t2 micro free tier
- select gp2 vlumn and 10 GB
- Enable auto scaling   
- We can setup authentication as username and psassword or IAM user 
- dwonalod and Use sql electron client
- create and test connection use mydb as db name 
- We can create replica/take snapshot by selection option under **Actions**
- while dleting DB remove enable accidentale deletion then delete it 
## 90. RDS custom for oracle and SQL server
- RDS Custom
  - Managed Oracle and Microsoft SQL Server DB with OS and DB customisation
  - RDS: Automates setup. operation and scaling of database in AWS
  - Custom: access to underlying database and OS so you can:
    - Configure setings
    - install patches
    - Enable native features
    - Access underlying EC2 instacne using SSH or SSM session manager
  - **De-activate automation Mode** to perform your customization, better to take DB snapshot before 
  - RDS vs RDS Custom
    - RDS: entire DB and the OS to be managed by AWS
    - RDS Custom: full admin access to the underlying OS and DB

## 91. Amazon Aurora
- Amazon Aurora
  - Aurora is a proprietary technology from AWS (not open sourced)
  - Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or MySQL database)
  - Aurora is “AWS cloud optimized” and claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS
  - Aurora storage automatically grows in increments of 10GB, up to 128 TB.
  - Aurora can have** 15 replicas** while **MySQL has 5**, and the replication process is faster (sub 10 ms replica lag)
  - Failover in Aurora is instantaneous. It’s HA (High Availability) native.
  - Aurora costs more than RDS (**20% more**) – but is more efficient
- Aurora High Availability and Read Scaling
  - 6 copies of your data across 3 AZ:
    - 4 copies out of 6 needed for writes
    - 3 copies out of 6 need for reads
    - Self healing with peer-to-peer replication
    - Storage is striped across 100s of volumes
  - One Aurora Instance takes writes (master)
  - Automated failover is handled for master in less than 30 seconds
  - Master + up to 15 Aurora Read Replicas serve reads
  - **Support for Cross Region Replication**
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/07d098b2-6b48-4804-9018-c26c0ed31751)
- Features of Aurora
  - Automatic fail-over
  - Backup and Recovery
  - Isolation and security
  - Industry compliance
  - Push-button scaling
  - Automated Patching with Zero Downtime
  - Advanced Monitoring
  - Routine Maintenance
  - Backtrack: restore data at any point of time without using backups
## 92. Amazon Aurora Handson
- Goto RDS and select aurora
- select mysql/postegres and version 
- Select dev/prod 
- set password
- select ec2 instance (ther is no free version)
- select option of reoplica
- select security group or cerate one
- select authentication method
- give db name 
- select backup retention
- enable backtrack upto 72 hrs
- we can sent log to cloudwatch
- click on creat button
- YOu cansee diferent instacne in differant AZs one main and oprher read replica and we will get two end points to connect
- we can add more reader to cluster and also creation cross region replica ir set a auto scaler policy for creating read replica by setting min and max capaticy
- delete reader instance first read instance and wtire and the database 
## 93. Amazon Aurora - Advanced concepts
- Aurora – Custom Endpoints
  - Define a subset of Aurora Instances as a Custom Endpoint
  - Example: Run analytical queries on specific replicas
  - The Reader Endpoint is generally not used after defining Custom Endpoints
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/150dc541-430a-4ca6-8a68-73029fb9d6ec)
- Aurora Serverless
  - Automated database instantiation and autoscaling based on actual usage
  - Good for infrequent,intermittent or unpredictable workloads
  - No capacity planning needed
  - Pay per second, can be more cost-effective
- Aurora Multi-Master
 - In case you want immediate failover for write node (HA) 
 -  Every node does R/W - vs promoting a RR as the new master
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/b02683b1-68ba-4eec-8b48-3176bd6357de)
- Global Aurora
  - Aurora Cross Region Read Replicas:
    - Useful for disaster recovery
    - Simple to put in place
  - Aurora Global Database (recommended):
    - 1 Primary Region (read / write)
    - Up to 5 secondary (read-only) regions, replication lag is less than 1 second
    - Up to 16 Read Replicas per secondary region
    - Helps for decreasing latency
    - Promoting another region (for disaster recovery) has an RTO of < 1 minute
    - **Typical cross-region replcation takes less than 1 second**
- Aurora Machine Learning
  - Enables you to add ML-based predictions to your applications via SQL
  - Simple, optimized, and secure integration between Aurora and AWS ML services
  - Supported services
  - Amazon SageMaker (use with any ML model)
  - Amazon Comprehend (for sentiment analysis)
  - You don’t need to have ML experience
  - Use cases: fraud detection, ads targeting, sentiment analysis, product recommendations
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/24fd871a-cbc9-47a3-8b82-4ea6bc8e6fc0)
## 94.RDS & Aurora - Backup and monitoring 
- RDS Backup
  - Backups are automatically enabled in RDS
  - Automated backups:
    - Daily full backup of the database (during the maintenance/backup window)
    - Transaction logs are backed-up by RDS every 5 minutes
    - => ability to restore to any point in time (from oldest backup to 5 minutes ago)
  - 1 to 35 days of retention, set 0 to disable automated backup
  - Manual DB Snapshots:
    - Manually triggered by the user
    - Retention of backup for as long as you want 
Trick: to save money in case you want to stop a RDS , then you still pay for storage. If you plan on stopping it for a long time, you should snapshot it & restore instead.

- Aurora Backup 
  - automated backups
    - 1 to 35 days ( can not be disabled)
    - point in time recovery in that timeframe
  - Manual DB snapshot
    - Manually triggered by the user 
    - Retension of backup for as long as you want
- RDS& Aurora restore options
  - Restoring a RDS/Aurora backup or a snapshot creates a new database 
  -  Restoring MySQL RDS database from s3
    - Creating a backup of your on-premises database
    - Store it on Amazon S3 (object store)
    - Restore the  backup file onto a new RDS instance running MySQL
  - Restoring MySQL AUrora cluster from S3
    - Create a backup of your on-premises databse using Percona XtraBackup
    - Store the backup file on Amazon S3
    - Restore the backup file onto a new Auroro cluster running MyySQL 
- AUrora Databasae cloning 
  - Create a new Aurora DB Cluster from an existing one 
  - Faster that snapshot & restore 
  - Uses copy-on-write protocol
    - Initially, then new DB cluster uses the same data volume as the orignal DB Cluster (fast and efficient - no copying is needed)
    - When usdates are made to the new DB cluster data, then additional storage is allocated and data is copied to be separated
- Very fast & cost effective
- Useful to create a staging database from a produciton datanbse without impacting the production database

## 95. RDS & Aurora Security
- At Rest Enctryption:
  - Database master & replicas encryption using AWS KMS - must be defined as launch time
  - If the master is not encrypted, the read replica cannot be encrypted
  - To encrypt an un-encrypted database, go through a DB snapshot & restore as encrypted
- In-flight encryption: TLS-ready by default, use the AWS TLS root certificateclient -side
- IAM Authentication: IAM riles to connect to your databse (Instead of username/pw)
- SEcurity groups: Control Network access to your RDS / Aurora DB
- No SSH available except on RDS Custom
- Audit Logs can be enabled and sent to CloudWatch Logs for longer retension
## 96. Amazon RDS Proxy
- Fully managed database proxy for RDS
- Allows apps to pool and share DB connections established with the DB
- Improving DB efficiency by reducing the stress on databse resources (E.g. CPU, RAM ) and minimize open connections (and timeouts)
- Serverless, autoscaling, HA multi-AZ
- Reduced RDS & Aurora failover time by up 66%
- Supprts RDS( Mysql, Postgrem MS SQL Server, MAria DB) and Aurora
- NO code chanhes required for mos apps
- Enforce IAM Authentication for DB, and secuerly store credentials in AWS Secrets Manager
- RDS Proxy is never publickly accessible (must be accessed form VPC)
## 97. Amazon ElastiCache Overview 
- Overview
  - The same way RDS is to get managed Relational Databases…
  - ElastiCache is to get managed Redis or Memcached
  - Caches are in-memory databases with really high performance, low latency
  - Helps reduce load off of databases for read intensive workloads
  - Helps make your application stateless
  - AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups
  - Using ElastiCache involves heavy application code changes
- DB Catche Example:
  -Applications queries ElastiCache, if not available, get from RDS and store in ElastiCache.
  - Helps relieve load in RDS
  - Cache must have an invalidation strategy to make sure only the most current data is used in there.
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/4ebba95a-96f0-4bad-bc3a-1360b532ec37)
- User session store example:
  - User logs into any of the application - The application writes
the session data into ElastiCache
  - The user hits another instance of our application
  - The instance retrieves the data and the user is already logged in 
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/2172b35a-543b-402a-8d51-9ee268e8650f)











