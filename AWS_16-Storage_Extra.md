# Section 16: AWS storage Extra
Link: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26099040#overview

## 171: AWS Snow Family Overview
- Highly-secure, portable devices to collect and process data at the edge,
and migrate data into and out of AWS
- Use case:
  - For Data migration: Snowcone, Snowball Edge and SnowMobile
  - For Edge computing: Snowconem and Snow Edge
- When to use :
  - Challenges:
  - Limited connectivity
  - Limited bandwidth
  - High network cost
  - Shared bandwidth (can’t maximize the line)
  - Connection stability
- Thub rule for AWS Snow Family: offline devices to perform data migrations
  - If it takes more than a week to transfer over the network, use Snowball devices!
- We need to request device from console, load data ship back to AWS
- Data Migration:
  - Snowball Edge (for data transfers)
    - Physical data transport solution: move TBs or PBs of data in or out
of AWS
    - Alternative to moving data over the network (and paying network fees)
    - Pay per data transfer job
    - Provide block storage and Amazon S3-compatible object storage
    - **Snowball Edge Storage Optimized**
      - 80 TB of HDD capacity for block volume and S3 compatible object storage
    - **Snowball Edge Compute Optimized**
      - 42 TB of HDD or 28TB NVMe capacity for block volume and S3 compatible object storage
    - Use cases: large data cloud migrations, DC decommission, disaster recovery
  - AWS Snowcone & Snowcone SSD
    - **Small, portable computing, anywhere, rugged & secure,
withstands harsh environments**
    - Light (4.5 pounds, 2.1 kg)
    - Device used for edge computing, storage, and data transfer
    - **Snowcone** – 8 TB of HDD Storage
    - **Snowcone SSD** – 14 TB of SSD Storage
    - Use Snowcone where Snowball does not fit (spaceconstrained environment)
    - Must provide your own battery / cables
    - Can be sent back to AWS offline, or connect it to internet and use** AWS DataSync** to send data
  - AWS Snowmobile
    - Transfer exabytes of data (1 EB = 1,000 PB = 1,000,000 TBs)
    - Each Snowmobile has 100 PB of capacity (use multiple in parallel)
    - High security: temperature controlled, GPS, 24/7 video surveillance
    - **Better than Snowball if you transfer more than 10 PB**
- Snow Family – Usage Process
  1. Request Snowball devices from the AWS console for delivery
  2. Install the snowball client / AWS OpsHub on your servers
  3. Connect the snowball to your servers and copy files using the client
  4. Ship back the device when you’re done (goes to the right AWS facility)
  5. Data will be loaded into an S3 bucket
  6. Snowball is completely wiped
- What is Edge Computing?
  - Process data while it’s being created on an edge location
  - A truck on the road, a ship on the sea, a mining station underground...
  - These locations may have
    - Limited / no internet access
    - Limited / no easy access to computing power
  - We setup a Snowball Edge / Snowcone device to do edge computing
  - Use cases of Edge Computing:
    - Preprocess data
    - Machine learning at the edge
    - Transcoding media streams
  - Eventually (if need be) we can ship back the device to AWS (for transferring data for example)
- Snow Family – Edge Computing
  - Snowcone & Snowcone SSD (smaller)
    - 2 CPUs, 4 GB of memory, wired or wireless access
    - USB-C power using a cord or the optional battery
  - **Snowball Edge – Compute Optimized**
    - 104 vCPUs, 416 GiB of RAM
    - Optional GPU (useful for video processing or machine learning)
    - 28 TB NVMe or 42TB HDD usable storage
  - **Snowball Edge – Storage Optimized**
    - Up to 40 vCPUs, 80 GiB of RAM, 80 TB storage
    - Object storage clustering available
  - All: Can run EC2 Instances & AWS Lambda functions (using AWS IoT Greengrass)
  - Long-term deployment options: 1 and 3 years discounted pricing
- AWS OpsHub
  - Historically, to use Snow Family devices, you needed a CLI (Command Line Interface tool)
  - Today, you can use **AWS OpsHub** (a software you install on your computer / laptop) to manage your Snow Family Device
    - Unlocking and configuring single or clustered devices
    - Transferring files
    - Launching and managing instances running on Snow Family Devices
    - Monitor device metrics (storage capacity, active instances on your device)
    - Launch compatible AWS services on your devices (ex: Amazon EC2 instances, AWS DataSync, Network File System (NFS))

## 172. AWS Snow Famliiy - Hands On
- Search AWS Snow Famlily
- Click on Order AWS device -> Give job Name -> select job type import/export
- Select DEvice - Select Pricing - Select storage tye like s3
- Select Ec2 for cmpute - slect s3 bucket
- Select IoT compatibility/ Remote access/control OPsHUb)
- Select Encryption TYpe
- Service rule for S3/SNS
- Shipping Address and speed
- SNS notificaiton Option

## 173. Snaball dat into Glacier : Solition Architecture
- Snowball cannot import to Glacier directly
- You must use Amazon S3 first, in combination with an S3 lifecycle policy to push dat into Glacier

## 174. Amazon FSx 
- Launch 3rd party high-performance file systems on AWS
- Fully managed service
- Types
  - FSx for Lustre
  - FSx for Windows File system
  - FSx for NetApp ON TAP
  - FSx for OpenZFS
- Amazon FSx for Windows (File Server)
  - **FSx for Windows** is a fully managed **Windows file system** share drive
  - Supports SMB protocol & Windows NTFS
  - Microsoft Active Directory integration, ACLs, user quotas
  - **Can be mounted on Linux EC2 instances**
  - Supports **Microsoft's Distributed File System (DFS) Namespaces** (group files across multiple FS)
  - Scale up to 10s of GB/s, millions of IOPS, 100s PB of data
  - Storage Options:
    - **SSD** – latency sensitive workloads (databases, media processing, data analytics, …)
    - **HDD** – broad spectrum of workloads (home directory, CMS, …)
  - Can be accessed from your on-premises infrastructure (VPN or Direct Connect)
  - Can be configured to be Multi-AZ (high availability)
  - Data is backed-up daily to S3
- Amazon FSx for Lustre
  - Lustre is a type of parallel distributed file system, for large-scale computing
  - The name Lustre is derived from “Linux” and “cluster
  - Machine Learning, **High Performance Computing (HPC)**
  - Video Processing, Financial Modeling, Electronic Design Automation
  - Scales up to 100s GB/s, millions of IOPS, sub-ms latencies
  - Storage Options:
    - SSD – low-latency, IOPS intensive workloads, small & random file operations
    - HDD – throughput-intensive workloads, large & sequential file operations
  - **Seamless integration with S3**
    - Can “read S3” as a file system (through FSx)
    - Can write the output of the computations back to S3 (through FSx)
  - **Can be used from on-premises servers (VPN or Direct Connect)**
- FSx Lustre - File System Deployment Options
  - **Scratch File System**
    - Temporary storage
    - Data is not replicated (doesn’t persist (backup) if file server fails)
    - High burst (6x faster, 200MBps per TiB)
    - Usage: short-term processing, optimize costs
  - **Persistent File System**
    - Long-term storage
    - Data is replicated within same AZ
    - Replace failed files within minutes
    - Usage: long-term processing, sensitive data
- Amazon FSx for NetApp ONTAP
  - Managed NetApp ONTAP on AWS
  - **File System compatible with NFS, SMB, iSCSI protocol**
  - Move workloads running on ONTAP or NAS to AWS
  - Works with:
    - Linux
    - Windows
    - MacOS
    - VMware Cloud on AWS
    - Amazon Workspaces & AppStream 2.0
    - Amazon EC2, ECS and EKS
  - Storage shrinks or grows automatically
  - Snapshots, replication, low-cost, compression and data de-duplication
  - **Point-in-time instantaneous cloning (helpful for testing new workloads)**
- Amazon FSx for OpenZFS
  - Managed OpenZFS file system on AWS
  - File System compatible with NFS (v3, v4, v4.1, v4.2)
  - Move workloads running on ZFS to AWS
  - Works with:
    - Linux
    - Windows
    - MacOS
    - VMware Cloud on AWS
    - Amazon Workspaces & AppStream 2.0
    - Amazon EC2, ECS and EKS
  - Up to 1,000,000 IOPS with < 0.5ms latency
  - Snapshots, compression and low-cost
  - **Point-in-time instantaneous cloning (helpful for testing new workloads)**

## 175. Amazon FSx - Hands on
- Search FSx -> create filesystem - select file system type
- filesystem name and select configuration and create

## 176. Storage Gateway Overview
- Hybrid Cloud for Storage
 - AWS is pushing for ”hybrid cloud”
    - Part of your infrastructure is on the cloud
    - Part of your infrastructure is on-premises
  - This can be due to
    - Long cloud migrations
    - Security requirements
    - Compliance requirements
    - IT strategy
  - S3 is a proprietary storage technology (unlike EFS / NFS), so how do you expose the S3 data on-premises?
  - AWS Storage Gateway!
- AWS Storage Cloud Native Options
  - Block: EBS and EC2 instance store
  - File: EFS and FSX
  - Object: s3 and Glacier
- AWS Storage Gateway
  - Bridge between on-premises data and cloud data
  - Use cases:
    - disaster recovery
    - backup & restore
    - tiered storage (IA data on cloud and FA data on prem)
    - on-premises cache & low-latency files access
  - Types of Storage Gateway:
    - S3 File Gateway
    - FSx File Gateway
    - Volume Gateway
    - Tape Gateway
- Amazon S3 File Gateway
  - Configured S3 buckets are accessible using the NFS and SMB protocol
  - **Most recently used data is cached in the file gateway**
  - Supports S3 Standard, S3 Standard IA, S3 One Zone A, S3 Intelligent Tiering
  - **Transition to S3 Glacier using a Lifecycle Policy**
  - Bucket access using IAM roles for each File Gateway
  - SMB Protocol has integration with Active Directory (AD) for user authentication
- Amazon FSx File Gateway
  - Native access to Amazon FSx for Windows File Server
  - Suitable when need for **Local cache for frequently accessed (FA)data**
  - Windows native compatibility (SMB, NTFS, Active Directory...)
  - Useful for group file shares and home directories
- Volume Gateway
  - Block storage using iSCSI protocol backed by S3
  - Backed by EBS snapshots which can help restore on-premises volumes!
  - **Cached volumes**: low latency access to most recent data
  - **Stored volumes**: entire dataset is on premise, scheduled backups to S3
- Tape Gateway
  - Some companies have backup processes using physical tapes (!)
  - With Tape Gateway, companies use the same processes but, in the cloud
  - Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
  - Back up data using existing tape-based processes (and iSCSI interface)
  - Works with leading backup software vendors
- Storage Gateway – Hardware appliance
  - Using Storage Gateway means you need on-premises virtualization
  - Otherwise, you can use a Storage Gateway Hardware Appliance
  - You can buy it on amazon.com
  - Works with File Gateway, Volume Gateway, Tape Gateway
  - Has the required CPU, memory, network, SSD cache resources
  - Helpful for daily NFS backups in small data centers

## 177. Storage Gateway - Hands On
- search gateway and explore all gateway options no need to create one

## 178. AWS Transfer Family
- Overview 
  - A fully-managed service for file transfers into and out of Amazon S3 or Amazon EFS using the FTP protocol
  - Supported Protocols
    - **AWS Transfer for FTP** (File Transfer Protocol (FTP))
    - **AWS Transfer for FTPS** (File Transfer Protocol over SSL (FTPS))
    - **AWS Transfer for SFTP** (Secure File Transfer Protocol (SFTP))
  - Managed infrastructure, Scalable, Reliable, Highly Available (multi-AZ)
  - Pay per provisioned endpoint per hour + data transfers in GB
  - Store and manage users’ credentials within the service
  - Integrate with existing authentication systems (Microsoft Active Directory, LDAP, Okta, Amazon Cognito, custom)
  - Usage: sharing files, public datasets, CRM, ERP, …

## 179 AWS DataSync - IMP
- Overview
  - Move large amount of data to and from
  - On-premises / other cloud to AWS (NFS, SMB, HDFS, S3 API…) – **needs agent**
  - AWS to AWS (different storage services) – no agent needed
  - Can synchronize to:
    - Amazon S3 (any storage classes – including Glacier)
    - Amazon EFS
    - Amazon FSx (Windows, Lustre, NetApp, OpenZFS...)
  - Replication tasks can be scheduled hourly, daily, weekly. Not real time
  - **File permissions and metadata are preserved (NFS POSIX, SMB…)**
  - One agent task can use 10 Gbps, can setup a bandwidth limit
- Snow family device can be used if network bandwidth is an issue

## 180. All AWS storage iptions compated
- S3: Object Storage
- S3 Glacier: Object Archival
- EBS volumes: Network storage for one EC2 instance at a time
- Instance Storage: Physical storage for your EC2 instance (high IOPS)
- EFS: Network File System for Linux instances, POSIX filesystem
- FSx for Windows: Network File System for Windows servers
- FSx for Lustre: High Performance Computing Linux file system
- FSx for NetApp ONTAP: High OS Compatibility
- FSx for OpenZFS: Managed ZFS file system
- Storage Gateway: S3 & FSx File Gateway, Volume Gateway (cache & stored), Tape Gateway
- Transfer Family: FTP, FTPS, SFTP interface on top of Amazon S3 or Amazon EFS
- DataSync: Schedule data sync from on-premises to AWS, or AWS to AWS
- Snowcone / Snowball / Snowmobile: to move large amount of data to the cloud, physically
- Database: for specific workloads, usually with indexing and querying

## Quiz:
1. You need to move hundreds of Terabytes into Amazon S3, then process the data using a fleet of EC2 instances. You have a 1 Gbit/s broadband. You would like to move the data faster and possibly processing it while in transit. What do you recommend?
- Use network 
- Use Snowcone
- Use AWS data migration
- **Use snowball Edge**
  - Snowball Edge is the right answer as it comes with computing capabilities and allows you to pre-process the data while it's being moved into Snowball.

2. You want to expose virtually infinite storage for your tape backups. You want to keep the same software you're using and want an iSCSI compatible interface. What do you use?
- AWS Snowball
- **AWS storage Gateway - Tape Gateway** 
- AWS storage Gateway - Volume Gateway
- AWS storage Gateway - S3 file Gateway

3. Your EC2 Windows Servers need to share some data by having a Network File System mounted on them which respects the Windows security mechanisms and has integration with Microsoft Active Directory. What do you recommend?
- **Amazon FSX windows (File Server)**
- Amazon EFS
- Amazon FSx for Lustre
- S3 File Gateway

4. You have hundreds of Terabytes that you want to migrate to AWS S3 as soon as possible. You tried to use your network bandwidth and it will take around 3 weeks to complete the upload process. What is the recommended approach to using in this situation?
- AWS storage Gsteway - Volume Gateway
- S3 Multipart Upload
- **AWS Snowball Edge**
- AWS Data Migration service

5. You have a large dataset stored in S3 that you want to access from on-premises servers using the NFS or SMB protocol. Also, you want to authenticate access to these files through on-premises Microsoft AD. What would you use?
- AWS Storage Gateway - Volume Gateway
- **AWS Storage Gateway - S3 File Gateway**
- AWS Storage Gateway - Tape Gateway
- AWS data Migration Service

6. You are planning to migrate your company's infrastructure from on-premises to AWS Cloud. You have an on-premises Microsoft Windows File Server that you want to migrate. What is the most suitable AWS service you can use?
- **Amazon FSx for windows (File Server)**
- AWS storage Gateway - s3 file gateway
- AWS managed Microsoft AD

7. You would like to have a distributed POSIX compliant file system that will allow you to maximize the IOPS in order to perform some High-Performance Computing (HPC) and genomics computational research. This file system has to easily scale to millions of IOPS. What do you recommend?
- EFS with Max. IO enabled
- **Amazon FSX for Lustre**
- Amazon S3 mounted on the EC2 instance
- EC2 instance Store

8. Which deployment option in the FSx file system provides you with long-term storage that's replicated within AZ?
- Scratch File System
- **Persistent File System** 
  - Provides long-term storage where data is replicated within the same AZ. Failed files were replaced within minutes.

9. Which of the following protocols is **NOT** supported by AWS Transfer Family?
- FTP
- FTPS
- Trasfer Lyer Security **TLS**
  - AWS Transfer Family is a managed service for file transfers into and out of S3 or EFS using the FTP protocol, thus TLS is not supported.
- SFTP


10. A company uses a lot of files and data which is stored in an FSx for Windows File Server storage on AWS. Those files are currently used by the resources hosted on AWS. There’s a requirement for those files to be accessed on-premises with low latency. Which AWS service can help you achieve this?
- S3 File Gateway
- FSX for Widnows File Server On-Prem
- **FSX File Gateway**
- Volumne Gateway

11. A Solutions Architect is working on planning the migration of a startup company from on-premises to AWS. Currently, their infrastructure consists of many servers and 30 TB of data hosted on a shared NFS storage. He has decided to use Amazon S3 to host the data. Which AWS service can efficiently migrate the data from on-premises to S3?
- AWS Storage Tape Gateway
- Amazon EBS
- AWS Transfer Family
- **AWS DataSync**

12. Which AWS service is best suited to migrate a large amount of data from an S3 bucket to an EFS file system?
- AWS Snowball
- **AWS dataSync**
- AWS Transfer Familuy
- AWS backup

13. A Machine Learning company is working on a set of datasets that are hosted on S3 buckets. The company decided to release those datasets to the public to be useful for others in their research, but they don’t want to configure the S3 bucket to be public. And those datasets should be exposed over the FTP protocol. What can they do to do the requirement efficiently and with the least effort?
- **Use AWS Transfer Famliy**
- Create an EC2 instance with FTP server installed then copy the data fron S3 to the EC2 instance
- Use AWS Storage Gateway
- Copy the data from S3 to an EFS file system, then expose them over the FTP protocol

14. Amazon FSx for NetApp ONTAP is compatible with the following protocols, EXCEPT ………………
- NFS
- SMB
- **FTP**
- iSCSI

15. Which AWS service is best suited when migrating from an on-premises ZFS file system to AWS?
- **Amazon FSX for Open ZFS**
- Amazon FSX for NetApp ONTAP
- Amazon FSX for Windows File Server
- Amazon FSX for Lustre

16. A company is running Amazon S3 File Gateway to host their data on S3 buckets and is able to mount them on-premises using SMB. The data currently is hosted on S3 Standard storage class and there is a requirement to reduce the costs for S3. So, they have decided to migrate some of those data to S3 Glacier. What is the most efficient way they can use to move the data to S3 Glacier automatically?
- Create a lambda fucntion to migeate data to S3 Glacier and periodically trigger it every day using Amazon EventBridge
- Use S3 Batch operation to loop through s3 files and move them to s3 glacier every day
- **Use S3 life cycle policy** 
- Use AWS DataSync to replicate data to S3 Glacier every day
- COnfigure s3 file Gteway to send the data to s3 Glacier directly

17. You have on-premises sensitive files and documents that you want to regularly synchronize to AWS to keep another copy. Which AWS service can help you with that?
- AWS Databse Migration service
- Amazon EFS
- **AWS DatSync**
  - AWS DataSync is an online data transfer service that simplifies, automates, and accelerates moving data between on-premises storage systems and AWS Storage services, as well as between AWS Storage services.

  









