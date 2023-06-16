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



























