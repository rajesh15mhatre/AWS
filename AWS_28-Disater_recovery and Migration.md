# Section 28: Disater Recovery and Migration
## 350. Disaster Recovery in AWS
## 351. Database migration Service (DMS)
## 352. Database MIgration Service - Hands on
## 353. RDS & Aurora Migration
## 354. On-premises Strategies with AWS 
## 355. AWS Backup
## 355. AWS Backup - Hands on
## 357. Application Migration Sevice(MGN)
- Evloution of CLoudEndure Migration service
## 358. Transferring Large Datasets into AWS
## 359. VMware Cloud on AWS
## Quiz
1. As part of your Disaster Recovery plan, you would like to have only the critical infrastructure up and running in AWS. You don't mind a longer Recovery Time Objective (RTO). Which DR strategy do you recommend?
- Backup and Restore
- **Pilot Light**
  - If you're interested, read more about Disaster Recovery options in AWS here: https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html
- Warm Standby
- Multi-Site

2. You would like to get the Disaster Recovery strategy with the lowest Recovery Time Objective (RTO) and Recovery Point Objective (RPO), regardless of the cost. Which DR should you choose?
- Backup and Restore
- Pilot Light
- Warm Standby
- **Multi-Site**
  - If you're interested, read more about Disaster Recovery options in AWS here: https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html

3. Which of the following Disaster Recovery strategies has a potentially high Recovery Point Objective (RPO) and Recovery Time Objective (RTO)?
- **Backup and Restore**
  - If you're interested, read more about Disaster Recovery options in AWS here: https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html
- Pilot Light
- Warm Standby
- Multi-Site

4. You want to make a Disaster Recovery plan where you have a scaled-down version of your system up and running, and when a disaster happens, it scales up quickly. Which DR strategy should you choose?
- Backup and Restore
- Pilot Light
- **Warm Standby**
  - If you're interested, read more about Disaster Recovery options in AWS here: https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html
- Multi-Site

5. You have an on-premises Oracle database that you want to migrate to AWS, specifically to Amazon Aurora. How would you do the migration?
- **Use AWS Schema COnversion Tool (AWS SCT) to conver the database schema, then use AWS Database Migration Service(DMS) to migrte the dat**a
- Use aws Databse MIgration Servie (AWS DMS) to converts the database schema, then use AWS sdchema cOnversion Tool (AWS SCT) to migrate Data

6. AWS DataSync supports the following locations, EXCEPT ....................
- S3
- **EBS**
- EFS
- FSx for Windows File Server

7. You are running many resources in AWS such as EC2 instances, EBS volumes, DynamoDB tables... You want an easy way to manage backups across all these AWS services from a single place. Which AWS offering makes this process easy?
- S3
- AWS Storage Gateway
- **AWS Backup**
  - AWS Backup enables you to centralize and automate data protection across AWS services. It helps you support your regulatory compliance or business policies for data protection.
- EC2 Snapshots

8. A company planning to migrate its existing websites, applications, servers, virtual machines, and data to AWS. They want to do a lift-and-shift migration with minimum downtime and reduced costs. Which AWS service can help in this scenario?
- AWS Database MIgration Service
- **AWS Application Migration Service**
- AWS Backup
- AWS Schema Sconversion Tool

9. A company is using VMware on its on-premises data center to manage its infrastructure. There is a requirement to extend their data center and infrastructure to AWS but keep using the technology stack they are using which is VMware. Which AWS service can they use?
   - **VMware Cloud AWS**
   - AWS DataSYnc
   - AWS Application Migration Service
   - AWS Application DIscovery SErvice

  10. A company is using RDS for MySQL as their main database but, lately, they have been facing issues in managing the database, performance issues, and scalability. They have decided to use Aurora for MySQL instead for better performance, less complexity and fewer administrative tasks required. What is the best way and most cost-effective way to migrate from RDS for MySQL to Aurora for MySQL?
- Raise an AWS support ticket to do the migration as it is not supported
- Create a database dump from RDS from MySQL, store it in an S3 Bucket, then restore it to Aurora for MySQL
- You can not migrate directly to Aurora for MySQL, you have to create a custom application to insert the data manually
- **Create a snapshot from RDS for MySQL and restore it to Aurora for MySQL**

11. Which AWS service can you use to automate the backup across different AWS services such as RDS, DynamoDB, Aurora, and EFS file systems, and EBS volumes?
- S3 lifecycle policies
- DataSync
- **Backup**
- Glacier
