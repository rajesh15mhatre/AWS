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
## RDS custom for oracle and SQL server
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















