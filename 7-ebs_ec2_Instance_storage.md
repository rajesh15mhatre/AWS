# Section 7: EC2 Instance Storage
Source: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098272#overview
# 55. EBS Overview
- An **EBS (Elastic Block Store)** Volume is a **network** drive you can attach to your instances while they run
- It allows your instance to persist data, even after their termination
- They can only be mounted to one instance at a time **(at CCP level)**
- Analogy: Think of them as a "network USB Stick"
- Free Tier: 30 GB of free EBS storage of type Genersl purpose (SSD) or magnetic per month

_Note: CCP - Certified Cloud Practitioner - one EBS can be only mounted to one EC2 instance
Associate Level(Solution Architect, Developer, SysOps): "multi-attach" feature for some EBS_
- EBS Volume
  - Its a network drive (not a physical drive)
    - It uses the network to communicate the instance, which means there might be a bit of latency
    - It can be detached from an EC2 instance and attched to another quickly
  - It's locked to an AZ
    - An EBS volume of us-east-1a can not to attched to us-east-1b
    - To move volume across AZm you first need to snapshot it
  - Have a provisioned capacity (size in GB, and IOPS)
    - You get billed for all the provisioned capacity
    - You can increase the capacity of the drive over time
  ![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/fc28ae71-3b81-4c2c-8aa0-93edf5801921)
  - EBS - Delete on Termination attribute
    - There is checkbox delete on temmination which we need to check 
    - BY default, the root EBS volume is deleted (which used while creating EC2 checkbox is cheked)
    - By default, t=any other attched EBS volumne is not delted (checkbox is unchked)
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/45e353b0-5173-4c9b-8c84-9f785a0f87ee)

## 56. EBS Hands on
- Goto to EC2 instance --> storage --> select ID under block devices 
- WE can create new EBS volumn on this page and its should be on same AZ when EC2 is ( we can check AZ unser naterworking tab of EC2)
- Create new EBS and attched to EC2 and you can see 2 volumes under block devices

## 57. EBS snapshots
- Make a backup (snapshot) of your EBS volume at a point in time
- Not necessary to detach volume to do snapshot, but recommended
- Can copy snapshots accross AZ or Region
- EBS Snapshot Archive 
  - MOve a snap to an "archive tier" that is 75% cheaper
  - Takes within 24 to 72 hours for restoring the archive
- Recycle BIn for EBS Snapshots
  - Setup rules to retain deleted snapshots so you can recover then after an accidental deletion 
  - Specify retention (frim 1 days to 1 year) 
- Fast Snapshot Resore (FSR)
  - Forcefull initialization of snapshot to have no latency on the first use ($$$)
  
## 58. EBS snapshot HandOn
- we can right click and copy snapshot then we canmove to any AZ/region helpful in case of disaster recovery 
- Goto actions and recycle bin and create a retention rule
- Select EBS volumne and creaet an snapshot 

## 59. AMI AMazon Machine Image
- AMI are a customization of an EC2 instance
  - You add your own software, cinfuguration, OSm Minitoring tools...
  - Faster boot/ config time as all your software is pre-packaged
- AMI are built for a specific region (and can be copied across regions)
- You can launch EC2 instances from:
  - A public AMI: AWS provided
  - Your own AMI: you make and maintail them yourself
  - An AWS marketplace AMI: an AMI someone else made (and potentiallly sells)
  - AMI Process (from EC2 instance)
    - Srat EC2 and customize it
    - Stop the instance ( for data integrity) 
    - Build an AMI - this will also create EBS snapshot
    - Launch instances from other AMIs
   
## 60. AMI Hands on
- Right click on EC2 instance --> image and templates -->create an image 
- Give name save it
- now you can use it to create EC2 from AMI

## 61. EC2 instance store
- EBS volumes are network drives with good but limited  performance
- If you need a high performance hardware disk, use EC2 instance store
- pros and cons
  - Better I/O performance
  - EC2 instance store lose their storage if they're stopped (ephemeral - short lasting)
  - good to buffer/ cache/ scratch data/ temporary content
  - Risk of data loss if hardware fails
  - Backups and replicas are users responsibilities
- I/O can reach upato 3mill whereas, EBS around 32k depending on machine cpu

## 62. EBS Volume types
- EBS Volume comes in 6 types
  - gp2 / gp3 (SSD): General puspose SSD volume that balances price and performance for a wide variety of workload
  - io1/ io2 (SSD): Highest performance SSD volume for mission critical low latency or high throughput workloads
  - st1 (HDD): Low cost HDD volume designed for frequently accessed, throughput-intensive workloads
  - sc1 (HDD): Lowest cost HDD volume designed for less frequently accessed workloads
- EBS volumes are characterized in size| throughput|IOPS
- When in doubt always consult the AWS documentation
- Only gp2/gp3 and io1/io2 can be used as boot volumes

![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/c4554b08-95a7-4d7b-b504-b8234a7aad43)

## 63. EBS multi-attach io1/io2 fanily
- Attach the same EBS volume to multiple EC2 instance in the same AZ
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/b7222d20-a971-4c13-9063-4d36733905a6)

- Each instance has full read & write permissions to the high-performance volume
- Use case:
  - Achive higher application  in clustered Linux application (ex. Teradata)
  - App must manage concurrent write operations
- Up to **16 EC2** Instance supports at a time
- Must use a file system that's cluster aware (not XFS, EXT4, etc)

## 64. EBS Encryption
- When you creat an encrypted EBS volume, you get the following:
  - Data at rest is encrypted inside the volume
  - All the data in flight moving between the instance and the volume is encrypted
  - All snapshots are encrypted
  - All volumes created from snapshot
- Encryption and decryption are handled transparently (you have nothing to do )
- Encryption has a minimal impact on latency
- EBS Encryption leverages key form KMS (AES-256)
- Copying an unexpected snapshot you need to enable encryption
- snapshot of encrypted volumes are encrypted
- Encryption: encrypt an unencrypted EBS volume
  - Create an EBS snapshot of the non-encrypted volume
  - Encrypt the EBS snapshot (using copy)
  - Create new EBS volume from the snapshot ( the volume will also be encrypted)
  - Now you can attached the encrypted volume to the origanal instance.

## EFS - Elastic file system
- Managed NFS (network file system) that can be mounted on many EC2 instances
- EFS works with EC2 instances in multi AZs
- Highly available, scalable, expensive (3x gp2), pay per use
- Use cases: content management, web serving, data sharing, wordpress
- Uses NFSv4.1 protocol
- Uses security group to control access to EFS
- **Compatible with Linux based AMI( not windows)**
- Encryption at rest using KMS
- POSIX file system (~linux) that has a standard file API
- File system scales automatically, pay-per-use, no capacity planning
- EFS - performance & storage classes
  - EFS scale
    - 1000s of concurrents NFS clients 10 GB+/s throuput
    - Grow to PB scale network file system, automatically
  - Performance Mode (set at EFS creation time)
    - Gernal purpose (default): latency sensetive use cases (web server,CMS etc.)
    - Max I/O: Higher latency, highly parallel(big data, media processing)
  - Throughput Mode
    - Bursting: 1 tB = 50MB/s + brust of up to 100MB/s
    - Provisioned : set your throughput regardless of storage size, ex.1 GB for 1 TB storage
    - Elastic: automatically scales throughput up or down based on your workloads 
      - Upto 3 gb for reads and 1 gb for writes
      - used for unpredicatable workloads
- EFS storage class
  - Storage tiers (lifecycle managemant feature move file after N days)
    - Standard(regional): for frequent accessed files
    - Infrequent access (EFS-IA): cost to retrive files, lower price to store. Enable EFS-IA with lifecycle policy (say move fie to IA when not used for 60 days)
  - Availibility and durability
    - standard: Multi-AZ, great for prod
    - One zone: One AZ, great for dev, backup enabled by default, compatible with IA (EFA One zone IA)
  - Over 90% in cost saving
## 66. EFS HAnds on
- Search EFS -> create new filesystem 
- customize-> given name 
- Standard is named as regional now
- create specific security group and assign it to all AZs
- check video for hands on
## EFS vs EFS 
- EBS Volumes
  - one instance (except multi-attched io1/io2)
  - are locked at AZ level
  - gp2: IO increases if the disk size increses 
  - io1: can increase IO independently
  - To migrate an EBS volume across AZ snaphot is needed
    - EBS backup use IO and you should't run them while your app is halding a lot of traffic
  - Root EBS volume of instance get terminated by default if the EC2 instance gets terminated ( you can disable that)
- EFS
  - Mounting 100s of instances accross AZs
  - EFS share website files (wordpress)
  - Only for Linux Instances (POSIX)
  - EFS has a higher proce point that EBS
  - Can leverage EFS-IA for cost saving
- Remember: EFS vs EBS vs Instance Store(Physical volme)

## EBS and EFS section cleanup
- into fielsystem --action - delelte
- ec2 instance -- teminated runnig istance
- EBS --> delte volum, snapsopts
- Network & security -> delete security groups

## Quiz
- You have just terminated an EC2 instance in us-east-1a, and its attached EBS volume is now available. Your teammate tries to attach it to an EC2 instance in us-east-1b but he can't. What is a possible cause for this?
  - Ans: EBS volumes are locked on an AZ
    - EBS Volumes are created for a specific AZ. It is possible to migrate them between different AZs using EBS Snapshots.
- You have launched an EC2 instance with two EBS volumes, Root volume type and the other EBS volume type to store the data. A month later you are planning to terminate the EC2 instance. What's the default behavior that will happen to each EBS volume?
  - The root volume wil be deleted and the EBS volume will not be deleted
    - By default, the Root volume type will be deleted as its "Delete On Termination" attribute checked by default. Any other EBS volume types will not be deleted as its "Delete On Termination" attribute disabled by default.
- You can use an AMI in N.Virginia Region us-east-1 to launch an EC2 instance in any AWS Region.
  - False
    - AMIs are built for a specific AWS Region, they're unique for each AWS Region. You can't launch an EC2 instance using an AMI in another AWS Region, but you can copy the AMI to the target AWS Region and then use it to create your EC2 instances.
- Which of the following EBS volume types can be used as boot volumes when you create EC2 instances?
  - gp1, gp2, io1, io2
    - When creating EC2 instances, you can only use the following EBS volume types as boot volumes: gp2, gp3, io1, io2, and Magnetic (Standard).
- What is EBS Multi-Attach?
  - What is EBS Multi-Attach?
    - Attach the same EBS volume to multiple EC2 instances in the same AZ
- You would like to encrypt an unencrypted EBS volume attached to your EC2 instance. What should you do?
  - Create an EBS snapshot of your EBS volume. Copy the snapshot and tick the option to encrypt the copied snashot. then, use that encrypted snapshot to create a new EBS volume.
- You have a fleet of EC2 instances distributes across AZs that process a large data set. What do you recommend to make the same data to be accessible as an NFS drive to all of your EC2 instances?
  - Use EFS
    -  EFS is a network file system (NFS) that allows you to mount the same file system on EC2 instances that are in different AZs.
- You would like to have a high-performance local cache for your application hosted on an EC2 instance. You don't mind losing the cache upon the termination of your EC2 instance. Which storage mechanism do you recommend as a Solutions Architect?
  - Instance store
    - EC2 Instance Store provides the best disk I/O performance.
- You are running a high-performance database that requires an IOPS of 310,000 for its underlying storage. What do you recommend?
  - Use an EC2 Instance store
    - You can run a database on an EC2 instance that uses an Instance Store, but you'll have a problem that the data will be lost if the EC2 instance is stopped (it can be restarted without problems). One solution is that you can set up a replication mechanism on another EC2 instance with an Instance Store to have a standby copy. Another solution is to set up backup mechanisms for your data. It's all up to you how you want to set up your architecture to validate your requirements. In this use case, it's around IOPS, so we have to choose an EC2 Instance Store.






























































































