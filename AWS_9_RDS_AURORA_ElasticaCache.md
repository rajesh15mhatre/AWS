# Section 9 AWS Fundamentals
## 87. Amazon RDS overview
- RDS stands for Relational Database Service
- It’s a managed DB service for DB uses SQL as a query language.
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
    - Continuous backups and restore to specific timestamps (Point in Time Restore)
    - Monitoring dashboards
    - Read replicas for improved read performance
    - Multi-AZ setup for DR (Disaster Recovery)
    - Maintenance windows for upgrades
    - Scaling capability (vertical and horizontal)
    - Storage backed by EBS (gp2 or io1)
  - But you can’t SSH into your DB instances, you can only use it as a DB service
- RDS Backups
  - Backups are automatically enabled in RDS
  - Automated backups:
    - Daily full backup of the database (during the maintenance window)
    - Transaction logs are backed up by RDS every 5 minutes
    - Ability to restore to any point in time (from oldest backup to 5 minutes ago)
    - 7 days retention (can be increased to 35 days)
  - DB Snapshots:
    - Manually triggered by the user
    - Retention of backup for as long as you want
- RDS - Storage Auto Scaling 
  - Helps you increase storage on your RDS DB instance dynamically
  - When RDS detects you are running out of free database storage, it scales automatically
  - Avoid manually scaling your database storage
  - You have to set **Maximum Storage Threshold** ( maximum limit for DB storage)
  - Automatically modify storage if:
    - Free storage is less than 10% of allocated storage
    - Low storage lasts at least 5 mins
    - 6 hours have passed since the last modification
  - Useful for applications with unpredictable workloads
  - Supports all RDS databases engines (MariaDB, MySQL, PostgreSQL, SQL Server, Oracle)

##  88. RDS Read replicas and Multi AZs (IMP)
- RDS Read Replicas for read scalability
  - Up to 5 Read Replicas
  - Within AZ, Cross AZ
or Cross Region
  - Replication is **ASYNC**, so reads are eventually consistent
  - Replicas can be promoted to their own DB
  - Applications must update the connection string to leverage read replicas
- RDS Read Replicas – Use Cases
  - You have a production database that is taking on a normal load
  - You want to run a reporting application to run some analytics
  - You create a Read Replica to run the new workload there
  - The production application is unaffected
  - Read replicas are used for SELECT (=read) only kind of statements (not INSERT, UPDATE, DELETE)
- RDS Read Replicas – Network Cost
  - In AWS there’s a network cost when data goes from one AZ to another
  - **For RDS Read Replicas within the same region, you don’t pay that fee but its chargeable when data is replicated in AZ of a different region**
- RDA Multi-AZ (Disaster recovery)
  - SYNC replication
  - One DNS name – automatic app failover to standby
  - Increase availability
  - Failover in case of loss of AZ, loss of network, instance, or storage failure
  - No manual intervention in apps
  - Not used for scaling
  - Multi-AZ replication is free
  - Note: The Read Replicas be setup as Multi-AZ for Disaster Recovery (DR)
- RDS - from single AZ to Multi-AZ
  - Zero downtime operation (no need to stop the DB)
  - Just click on “modify” for the database
  - The following happens internally:
    - A snapshot is taken
    - A new DB is restored from the snapshot in a new AZ
    - Synchronization is established between the two databases

## 89. Amazon RDS Hands-on
- goto RDS - create DB - select engine 
- selct muysql ansd version
- select template - production
- under availability and durability
  -  Single DB instance/ Multi/ Multi cluster
- Select db t2 micro free tier
- select gp2 volumn and 10 GB
- Enable auto-scaling   
- We can set up authentication as username and password or IAM user 
- download and use sql electron client
- create and test connection - use mydb as db name 
- We can create replicas/take snapshots by selection option under **Actions**
- while deleting DB remove enable accidental deletion then delete it 
## 90. RDS custom for Oracle and SQL server
- RDS Custom
  - Managed Oracle and Microsoft SQL Server DB with OS and DB customization
  - RDS: Automates setup. operation and scaling of the database in AWS
  - Custom: access to the underlying database and OS so you can:
    - Configure settings
    - install patches
    - Enable native features
    - Access underlying EC2 instance using SSH or SSM session manager
  - **De-activate automation Mode** to perform your customization, better to take DB snapshot before 
  - RDS vs RDS Custom
    - RDS: entire DB and the OS to be managed by AWS
    - RDS Custom: full admin access to the underlying OS and DB

## 91. Amazon Aurora
- Amazon Aurora
  - Aurora is a proprietary technology from AWS (not open-sourced)
  - Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or MySQL database)
  - Aurora is “AWS cloud-optimized” and claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS
  - Aurora storage automatically grows in increments of 10GB, up to 128 TB.
  - Aurora can have** 15 replicas** while **MySQL has 5**, and the replication process is faster (sub 10 ms replica lag)
  - Failover in Aurora is instantaneous. It’s HA (High Availability) native.
  - Aurora costs more than RDS (**20% more**) – but is more efficient
- Aurora High Availability and Read Scaling
  - 6 copies of your data across 3 AZ:
    - 4 copies out of 6 needed for writes
    - 3 copies out of 6 needed for reads
    - Self-healing with peer-to-peer replication
    - Storage is striped across 100s of volumes
  - One Aurora Instance takes writes (master)
  - Automated failover is handled for the master in less than 30 seconds
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
## 92. Amazon Aurora Hands-on
- Goto RDS and select aurora
- select MySQL/Postgres and version 
- Select dev/prod 
- set password
- select ec2 instance (there is no free version)
- select the option of a replica
- select a security group or create one
- select the authentication method
- give db name 
- select backup retention
- enable backtrack upto 72 hrs
- we can send a log to Cloudwatch
- click on Create button
- You can see different instances in different AZs one main and another read replica and we will get two endpoints to connect
- we can add more readers to the cluster and also create cross-region replicas or set an auto scaler policy for creating read replicas by setting min and max capacity
- delete reader instance first read instance and write and the database 
## 93. Amazon Aurora - Advanced concepts
- Aurora – Custom Endpoints
  - Define a subset of Aurora Instances as a Custom Endpoint
  - Example: Run analytical queries on specific replicas
  - The Reader Endpoint is generally not used after defining Custom Endpoints
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/150dc541-430a-4ca6-8a68-73029fb9d6ec)
- Aurora Serverless
  - Automated database instantiation and autoscaling based on actual usage
  - Good for infrequent, intermittent or unpredictable workloads
  - No capacity planning is needed
  - Pay per second, can be more cost-effective
- Aurora Multi-Master
 - In case you want immediate failover for the write node (HA) 
 -  Every node does R/W - vs promoting a RR as the new master
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/b02683b1-68ba-4eec-8b48-3176bd6357de)
- Global Aurora
  - Aurora Cross Region Read Replicas:
    - Useful for disaster recovery
    - Simple to put in place
  - Aurora Global Database (recommended):
    - 1 Primary Region (read/write)
    - Up to 5 secondary (read-only) regions, replication lag is less than 1 second
    - Up to 16 Read Replicas per secondary region
    - Helps for decreasing latency
    - Promoting another region (for disaster recovery) has an RTO of < 1 minute
    - **Typical cross-region replication takes less than 1-second**
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
    - Transaction logs are backed up by RDS every 5 minutes
    - => ability to restore to any point in time (from oldest backup to 5 minutes ago)
  - 1 to 35 days of retention, set 0 to disable automated backup
  - Manual DB Snapshots:
    - Manually triggered by the user
    - Retention of backup for as long as you want 
Trick: to save money in case you want to stop an RDS, then you still pay for storage. If you plan on stopping it for a long time, you should snapshot it & restore it instead.

- Aurora Backup 
  - automated backups
    - 1 to 35 days ( can not be disabled)
    - point in time recovery in that timeframe
  - Manual DB snapshot
    - Manually triggered by the user 
    - Retention of backup for as long as you want
- RDS& Aurora restore options
  - Restoring an RDS/Aurora backup or a snapshot creates a new database 
  -  Restoring MySQL RDS database from s3
    - Creating a backup of your on-premises database
    - Store it on Amazon S3 (object store)
    - Restore the  backup file onto a new RDS instance running MySQL
  - Restoring MySQL Aurora cluster from S3
    - Create a backup of your on-premises database using Percona XtraBackup
    - Store the backup file on Amazon S3
    - Restore the backup file onto a new Auroro cluster running MySQL 
- Aurora Database cloning 
  - Create a new Aurora DB Cluster from an existing one 
  - Faster than snapshot & restore 
  - Uses copy-on-write protocol
    - Initially, the new DB cluster uses the same data volume as the original DB Cluster (fast and efficient - no copying is needed)
    - When updates are made to the new DB cluster data, then additional storage is allocated and data is copied to be separated
- Very fast & cost-effective
- Useful to create a staging database from a production database without impacting the production database

## 95. RDS & Aurora Security
- At Rest Encryption:
  - Database master & replicas encryption using AWS KMS - must be defined as launch time
  - If the master is not encrypted, the read replica cannot be encrypted
  - To encrypt an un-encrypted database, go through a DB snapshot & restore it as encrypted
- In-flight encryption: TLS-ready by default, use the AWS TLS root certificate client -side
- IAM Authentication: IAM roles to connect to your database (Instead of username/pw)
- Security groups: Control Network access to your RDS / Aurora DB
- No SSH available except on RDS Custom
- Audit Logs can be enabled and sent to CloudWatch Logs for longer retention
## 96. Amazon RDS Proxy
- Fully managed database proxy for RDS
- Allows apps to pool and share DB connections established with the DB
- Improving DB efficiency by reducing the stress on database resources (E.g. CPU, RAM ) and minimizing open connections (and timeouts)
- Serverless, autoscaling, HA multi-AZ
- Reduced RDS & Aurora failover time by up 66%
- Supprts RDS( Mysql, Postgrem MS SQL Server, MAria DB) and Aurora
- NO code changes are required for most apps
- Enforce IAM Authentication for DB, and securely store credentials in AWS Secrets Manager
- RDS Proxy is never publicly accessible (must be accessed from VPC)
## 97. Amazon ElastiCache Overview 
- Overview
  - The same way RDS is to get managed Relational Databases…
  - ElastiCache is to get managed by Redis or Memcached
  - Caches are in-memory databases with really high performance, low latency
  - Helps reduce the load off of databases for read-intensive workloads
  - Helps make your application stateless
  - AWS takes care of OS maintenance/patching, optimizations, setup, configuration, monitoring, failure recovery, and backups
  - Using ElastiCache involves heavy application code changes
- DB Cache Example:
  -Applications queries ElastiCache, if not available, get from RDS and store in ElastiCache.
  - Helps relieve load in RDS
  - Cache must have an invalidation strategy to make sure only the most current data is used there.
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/4ebba95a-96f0-4bad-bc3a-1360b532ec37)
- User session store example:
  - User logs into any of the applications - The application writes
the session data into ElastiCache
  - The user hits another instance of our application
  - The instance retrieves the data and the user is already logged in 
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/2172b35a-543b-402a-8d51-9ee268e8650f)
- ElastiCache – Redis vs Memcached
  - Redis
    - **Multi AZ** with Auto-Failover
    - **Read Replicas** to scale reads and have **high availability**
    - Data Durability using AOF(Append only File) persistence
    - **Backup and restore features**
  - MEMCACHED
    - Multi-node for partitioning of data (sharding)
    - **No high availability (replication)**
    - **Non persistent**
    - **No backup and restore**
    - Multi-threaded architecture 
## 98. ElastiCache Hands-On
-Search ElastiCache -> select Creat Cluster (redis/MemCache)
- Disabled cluster mode for free access
- Given name to Cluster info
- Location -> AWS Cloud
- MultiAZ-> Disabled (free)
- AutoFailover -> enable
- Select Node type -> t2micro
- zero replica
- Give subnet name -> select vpc
- Disable - ENcrypt in transit We can Select access control - Redis AUTH if enabled and provide the password 
- Click Create
- Open CacheCLuster
  - we can use the primary/reader endpoint in our app to read the cache
- After the demo, delete it with no backup option

## 99. ElastiCache for Solution Architect
All caches in ElastiCache:
- ElastiCache supports **IAM authentication for Redis**
- IAM policies on ElastiCache are only used for
AWS API-level security
- Redis AUTH
  - You can set a “password/token” when you create a Redis cluster
  - This is an extra level of security for your cache (on top of security groups)
  - Support SSL in-flight encryption
- Memcached
  - Supports SASL-based authentication (advanced)
- Patterns for ElastiCache
  - **Lazy Loading:** All the read data is cached, data can become stale in the cache
  - **Write Through:** Adds or updates data in the cache when written to a DB (no stale data)
  - **Session Store:** Store temporary session data in a cache (using TTL features)
  - Quote: There are only two hard things in Computer Science: cache invalidation and naming things
- ElastiCache – Redis Use Case
  - Gaming Leaderboards are computationally complex
  - Redis Sorted sets guarantee both uniqueness and element ordering
  - Each time a new element is added, it’s ranked in real-time, then added in the correct order


## 100. List of ports
- **List of Ports to be familiar with**
Here's a list of standard ports you should see at least once. You shouldn't remember them (the exam will not test you on that), but you should be able to differentiate between an Important (HTTPS - port 443) and a database port (PostgreSQL - port 5432) 

Important ports:
FTP: 21

SSH: 22

SFTP: 22 (same as SSH)

HTTP: 80

HTTPS: 443

vs RDS Databases ports:

PostgreSQL: 5432

MySQL: 3306

Oracle RDS: 1521

MSSQL Server: 1433

MariaDB: 3306 (same as MySQL)

Aurora: 5432 (if PostgreSQL compatible) or 3306 (if MySQL compatible)

Don't stress out on remember those, just read that list once today and once before going into the exam and you should be all set :)

Remember, you should just be able to differentiate an "Important Port" vs an "RDS database Port".

