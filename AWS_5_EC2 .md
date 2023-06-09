# AWS Solution Architect Associate (SAA-003)
# Section 5: EC2 Fundamentals
## 30.	AWS budget setup
-	Go to Account -> select billing from left navigation.
-	Select billing budget like zero spending budget.
## 31.	EC2 Basics
-	Introduction
  -	 EC2 is one of the most popular offerings of the AWS
  -	EC2=Elastic Compute Cloud = Infrastructure as service
  -	It mainly consists in capability of:
    -	Renting virtual machines (EC2 instance) 
    -	Storing data on virtual drives (EBS)
    -	Distributing load across machines(ELB)
    -	Scaling the services using an auto scaling group (ASG)
    -	Knowing the EC2 is knowing how cloud works.
-	EC2 sizing and configuration options:
  -	Operating systems(OS): Linux, Mac, Windows
  -	How much compute power and cores (CPU)
  -	How much random-access memory (RAM)
  -	How much storage space:
    -	Network attached (EBS & EFS)
    -	Hardware (EC2 Instance store)
    -	Network card: Speed of the card, Public IP address
    -	Firewall rules: security group
    -	Bootstrap Script (Configure at fist launch) : EC2 User data script.
-	EC2 User Data
  -	It is possible to bootstrap our instance using EC2 User data script.
  -	bootstrapping means launching commands when machine starts.
  -	That script is only run once at the instance first starts.
  -	EC2 instance data is used to automate boot task such as:
    -	Installing update
    -	Installing software
    -	Downloading common files from Internet
    -	Anything you can think of
  -	The EC2 user data runs with the root user.
  -	EC2 instance Type
    -	t2.micro is free tier with 750 hours per limit.
## 32.	Create EC2 instance for webserver with user data script.
-	Launch EC2 instance.
  -	Goto EC2 -> click on Instances from navigation.
  -	Click on “Launch Instances” button.
  -	Add Name and tag
  -	Select OS Image “Amazon Linux” from quick start.
  -	Select “Instance Type” configuration of machine.
    -	t2.micro which is free tier.
  -	Create a key pair for access keys.
    -	Give name to key pairs.
    -	Select type – RSA Encrypted (supported in windows)
    -	Select file format. 
      -	.pem (Uses openssh) for MAC, Linux, and windows 10  or latest .
      -	.ppk (Uses putty)  older than Windows 10
    -	Create Keys
  - Go to Network settings.
    -	Select “Allow network traffic from” checkbox and select  “0.0.0.0/0” in dropdown; means anywhere.
    -	Select “Allow traffic from Internet” checkbox which is http.
  -	Configure storage:
    -	Keep default 8 GB.
    -	Delete volume on terminate is default option. so, storage will be deleted when instance is terminated.
  -	Put below script under user data section which is at the end. This will be executed when first instance is started.
  - Select 1 instance and click on launch instance.
-	Click on “view my instances”.
-	Click on instance and we will get instance details below.
-	We will be using public IP to access EC2.
-	Copy public address in browser http://134.23.23.34 to access
-	On console select instance state dropdown from top to stop instance to stop server. We can also terminate instance using option.
-	Public IP changes whenever we restart instance.

## 33.	EC2 instance type – Overview
-	Check different types of EC2 instances.
  -	https://aws.amazon.com/ec2/instance-types/
- Naming conventions : m5.2xlarge
  -	m: Instance class
  -	5: generation (AWS improves them over time)
  -	2xlarge: size within an instance class
-	General purpose Type
  -	Great for diversity of workload such as web servers and code repositories
  -	Balance between:
    -	Compute
    -	Memory
    -	Networking
  -	In the course, we will use t2.micro which is general purpose EC2 instance.
-	Compute optimized type.
  -	Great for compute intensive task that require high performance processor.
    -	Batch processing workload
    -	Media transcoding
    -	High performance webservers
    -	High performance computing (HPC)
    -	Scientific modelling and machine learning
    -	Dedicated gaming servers
  -	Starts with name c5*. Instance class is c.
-	Memory optimized type.
  -	Fast performance for workload that process large datasets in memory.
  -	Use cases.
    -	High performance relational/non-relational databases
    -	Distributed web scale cache stores
    -	In-memory databases optimized for BI (Business Intelligence)
    -	Application performing real time processing of big unstructured data.
  -	Starts with name r*
-	Storage Optimized type:
  -	Great for storage intensive task that require high, sequential read and write access to large datasets on local storage.
  	Use cases:
    -	High frequency Online Transactional Processing (OLTP) systems.
    -	Relational or NoSQL databases
    -	Cache or in-memory databases like Redis
    -	Data warehousing applications
    -	Distributed filesystems.
-	Compare different EC2 instances on below website:
  -	https://ec2instances.github.io/
## 34.	Security Groups and classic port overview
-	Introduction
  -	Security Groups are the fundamental of network security in AWS.
  -	They control how traffic is allowed into or out of our EC2 instance.
  -	Security group only contain allow rules.
  -	Security group can reference by IP or by security groups.
-	Security Group deep dive
  -	Security groups are act like a firewall on EC2 instance.
  -	They regulate:
    -	Access to ports
    -	Authorise IP ranges – IPv4 or IPv6
    -	Control of inbound network (From outside to Instance)
    -	Control of outbound network (From instance to outside)
-	Security groups good to know:
  -	It can be attached to multiple instances.
  -	Lock down to a region / VPC combination- If you change your region you need to create separate security group.
  -	It does live outside of EC2  - if traffic is blocked the EC2 instance won’t see it.
  -	It’s good to maintain one separate security group for SSH access.
  -	If your application is not accessible (timeout), then it’s a **security group issue.**
  -	If your application gives “connection refuse” error, then its an **application error** or its not launched.
  -	All inbound traffic is blocked by default.
  -	All outbound traffic is authorised by default.
  -	Advance feature: EC2 with similar security groups are allowed to communicate whereas others are not:
-	Classic ports to know:
  -	22 – SSH (Secured shell) – Log into a Linux instance
  -	21 – FTP (File Transfer Protocol) – Upload file into file share
  -	22 – SFTP (Secure File Transfer Protocol) – Upload file using SSH
  -	80 – HTTP – Access unsecured websites.
  -	443 – HTTPS – Access secured websites.
  -	3389 – RDP (Remote Desktop Protocol) – Log in into a Windows instance

## 35.	Security Groups Hands on
-	Got EC2 -> From navigation under “Network & Security” click on “Security Groups”
-	Go to Inbound and Outbound to create security groups.

## 36.	SSH Overview
-	We can either use SSH command line software or a EC2 Instance connect (web browser) as OS:

## 37.	How to SSH - Linux and Mac
-	SSH is the most important function. It allows you to control remote machine , all using command line.
-	Steps:
  -	Remove any space from .pem file name.
  -	Get public IPv4 address of EC2 instance from console.
  -	Ensure you have inbound rule on EC2 to allow access to SSH on port 22 from anywhere. if not then click on security group and add a rule.
  -	Use below command to connect to EC2. 
  -	Before using this command, we need to give permission to key file using chmod 0400 tutorial.pem otherwise will get a permission error.
    -	ssh -i tutorial.pem ec2-user@23.23.23.234
    -	‘-I tutorial.pem’ is to key file for connection. Will get an error (too many authentications failure) if we do not pass this key.
    -	here ‘ec2-user’ is default user created by EC2.
    -	IP is public IPv4 of EC2
  -	Use Ctrl+C to terminate process and Ctrl+Z to exit from EC2

## 38.	How to SSH – prior version of Windows 10
-	Download putty software and install it.
-	Putty has putty and putty gen software.
-	Open putty gen 
  -	Click on Load button.
  -	Select EC2_tutorial.pem file 
  -	Click on save private key as EC2Tutorial.ppk. 
-	Open putty app
  -	Enter user and IPv4 from EC2 console. ec2-user@23.23.23.234
  -	Enter name under saved session text box.
  -	From navigation Select SSH->Auth
  -	Browse and select private key we generated. 
  -	Go back to session click on save so all settings will be saved.
  -	Click on open. 
  -	Always select session – load and then open to access EC2
## 39.	How to SSH – Windows 10 and newer
-	From powershell or CMD check if `ssh` is available 
  -	ssh -i ./tutorial.pem ec2-user@23.23.23.234
-	Enter Yes to trust the keys
-	In case of file permission error right click on file -> properties -> security -> give permissions

## 40.	Notes:
-	SSH Troubleshooting

  - There's a connection timeout
This is a security group issue. Any timeout (not just for SSH) is related to security groups or a firewall. Ensure your security group looks like this and correctly assigned to your EC2 instance.

  - There's still a connection timeout issue
If your security group is properly configured as above, and you still have connection timeout issues, then that means a corporate firewall or a personal firewall is blocking the connection. Please use EC2 Instance Connect as described in the next lecture.

  - SSH does not work on Windows
If it says: ssh command not found, that means you have to use Putty
Follow again the video. If things don't work, please use EC2 Instance Connect as described in the next lecture.

  - There's a connection refused
This means the instance is reachable, but no SSH utility is running on the instance
Try to restart the instance.
If it doesn't work, terminate the instance and create a new one. Make sure you're using Amazon Linux 2

  - Permission denied (publickey,gssapi-keyex,gssapi-with-mic)
This means either two things:
You are using the wrong security key or not using a security key. Please look at your EC2 instance configuration to make sure you have assigned the correct key to it.
You are using the wrong user. Make sure you have started an Amazon Linux 2 EC2 instance, and make sure you're using the user ec2-user. This is something you specify when doing ec2-user@<public-ip> (ex: ec2-user@35.180.242.162) in your SSH command or your Putty configuration.


  - Nothing is working - "aaaahhhhhh"
Don't panic. Use EC2 Instance Connect from the next lecture. Make sure you started an Amazon Linux 2 and you will be able to follow along with the tutorial :)

  - I was able to connect yesterday, but today I can't
This is probably because you have stopped your EC2 instance and then started it again today. When you do so, the public IP of your EC2 instance will change. Therefore, in your command, or Putty configuration, please make sure to edit and save the new public IP.

## 41.	EC2 Instance Connect
-	Click on EC2 instance -> from top click on ‘connect’ button.
-	Select EC2 instance connect tab -> click on ‘connect’ button from bottom.
-	It’s a terminal in browser.
-	It requires SSH outbound rule for IPv4 and sometimes for IPv6. So, add this rule.
## 42.	EC2 instance Role Demo
-	Connect to EC2
-	We configure aws using below command, however it is not a best practice instead we should create a IAM roles.
  - `aws configure`
-	Create a IAM role or attach existing one. 
  -	Goto EC2 -> Select “security->modify IAM role” from action dropdown on top.
  -	Choose IAM role -> save
-	Test IAM access using below command:
  -	aws iam list-users (sometimes policy attachment activity takes few seconds)
## 43.	EC2 instance purchasing options:
-	EC2 On-Demand:
  - Pay for what you use:
    - Linux or Windows – billing per second, after first minute
    -	All other OS – Billing per hour
  -	Has highest cost but no upfront payment
  -	No long- term commitment
  -	Recommended for short-term and un-interrupted workload, where you can’t predict how the application will behave.
-	EC2 Reserved Instances:
  -	Upto 72% discount compared to On-demand instances.
  -	You reserve specific instance attributes (Instance Type, Region, Tenancy, OS)
  -	Reservation Period – 1 year (+discount) or 3 years (+++discount)
  -	Payment options – No upfront (+discount), Partial upfront (++discount), All upfront (++++discount)
  -	Reserved Instance’s Scope – Regional or Zonal ( reserve capacity in an AZ)
  -	Recommended for steady-state usages applications (like database)
  -	You can buy and sell in the reserved Instance Marketplace
  -	Convertible Reserve Instance:
    -	can change the EC2 instance type, instance family, OS, scope and tenancy
    -	Up to 66% discount
-	EC2 Savings Plans
  -	Get a discount based on long-term usage ( up to 72% - same as RIs)
  -	Commit to a certain type of usage (10$/hour for 1 or 3 years)
  -	Usage beyond EC2 savings Plans is billed at the On-Demand price.
  -	Locked to a specific instance family & AWS region (e.g. M5 in us-east-1)
  -	Flexible across:
    -	Instance Size (e.g. m5.xlarge, m5.2xlarge)
    -	OS (Linux, Windows)
    -	Tenancy ( Host, Dedicated, Default)
-	EC2 Spot Instances
  -	Can get a discount of up to 90% compared to On-Demand
  -	Instance that you can lose at any point of time if your max price is less than the current spot price
  -	The MOST cost-efficient instances in AWS
  -	Useful for workloads that are resilient to failure:
    -	batch jobs
    -	Data Analysis
    -	Image Processing
    -	Any Distributed workload
    -	Workloads with a flexible start and end time
-	Not suitable for critical jobs or databases
-	EC2 Dedicated Hosts 
  -	A Physical server with EC2 instance capacity fully dedicated to your use.
  -	Allows you address compliance requirements and use your existing server-bound
software licenses (per-socket, per-core, pre-VM software licenses)
  -	Purchasing Options:
    -	On-demand – pay per second for Dedicated Host
    -	Reserved – 1 or 3 year(No Upfront, Partial upfront, All Upfront)
  -	Most Expensive
  -	Useful for software that have complicated licensing model(BYOL – Bring Your Own License)
  -	Or for companies that have strong regulatory, or compliance needs]
-	EC2 Dedicated Instances
  -	Instance run on hardware that’s dedicated to you.
  -	May share hardware with other instances in same account.
  -	No control over instance placement ( can move hardware after Stop/Start)
-	EC2 Capacity Reservations
  -	Reserve On-Demand instances capacity in a specific AZ for any duration
  -	You always have access to EC2 capacity when you need it
  -	No time commitment (create/cancel anytime), no billing discounts
  -	Combine with Regional Reserved Instances and Savings Plan  to benefit from billing discounts.
 -	 You’re charged at On-Demand rate Whether you run instance or not.
  -	Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ.
## 44.	Spot Instance Request
- You can get upto 90% discount to on-demand
Define max spot price and get the instance while current spot price < max 
  - The hourly spot price varies based on offer and capacity
  - IF the current spot price > your max proce you choose to stop or terminate your instance with a 2 minutes grace period
- Other strategy: spot block (Depricatd after DEc 2022 by AWS)
  - "block" spot intance during a specified time frame (1 to 6 hours) without interruption 
  - In rere situation, the instance may be reclaimed
- Used for batch jobs, data analysis, or workloads that are resillient to failuers.
- Not greate for critical process or databses.
- How to terminate spot instances
  - instance needs to be in any of these states - open, active or disabled in order to terminate it
- Spot Fleets
  - Set of Spot Instances + (Optional) On-Demand Instances
  - It will try to meeet the target capacity woith price constraints
    - Define possible launch pools; instacne type(m5.large), OS, AZ
    - Can have multiple launch poolsm so that the fleet can choose
    - Spot Fleet stops launching instances when reaching capaclity or max max cost
 - Strategies to allocate Spot Instances
    - Lowest Price: from the pool with the lowest price (cost optimization, short workloads)
  - diversified: distributed across all pool (great for availibility, long workload)
  - capacityOptimized: pool with the optimal capacity for the number of instances
  - priceCapacityOptimized:(recommended) pools with highest capacity available, then select the pool with the lowest proce (best chosice for most workloads)
- Spot fleets allows us to automatically request Spot Instances with the lowest price.

## 45. EC2 Instacne Launcg Types Hands On:
- Explore various option in spot INstacnes
- We can also request spot instance while creating normal instance
  - Just select spot instance chekbox in advance settign while creating instance
  - It will by default keep your max price equals to on-Demand which you can ,customize as well.
  - You can select request type ( One time/Persistant)
  - for persistance select data range and action (teminate/hibernate/stop)when instance proce goes up that your set price.
## Quiz
- Which EC2 Purchasing Option can provide you the biggest discount, but it is not suitable for critical jobs or databases?
  - Spot Instances
- What should you use to control traffic in and out of EC2 instances?
  - Security Groups
- How long can you reserve an EC2 Reserved Instance?
  - 1 or 3 years
- You would like to deploy a High-Performance Computing (HPC) application on EC2 instances. Which EC2 instance type should you choose?
  - Compute Optimized
- Which EC2 Purchasing Option should you use for an application you plan to run on a server continuously for 1 year?
  - Reserved Instance
- You are preparing to launch an application that will be hosted on a set of EC2 instances. This application needs some software installation and some OS packages need to be updated during the first launch. What is the best way to achieve this when you launch the EC2 instances?
  - Write a batch script that installs the required software and updates to your OS, then use this script in EC2 User Data when you launch your EC2 instance.
- Which EC2 Instance Type should you choose for a critical application that uses an in-memory database?
  - Memory optimized
- You have an e-commerce application with an OLTP database hosted on-premises. This application has popularity which results in its database has thousands of requests per second. You want to migrate the database to an EC2 instance. Which EC2 Instance Type should you choose to handle this high-frequency OLTP database?
  - Storage Optimized
- Security Groups can be attached to only **one EC2 instance**.
  - False ( Security Groups can be attached to multiple EC2 instances within the same AWS Region/VPC.)
- You're planning to migrate on-premises applications to AWS. Your company has strict compliance requirements that require your applications to run on dedicated servers. You also need to use your own server-bound software license to reduce costs. Which EC2 Purchasing Option is suitable for you?
  - DEdicated Hosts (Dedicated Hosts are good for companies with strong compliance needs or for software that have complicated licensing models. This is the most expensive EC2 Purchasing Option available.)
- You would like to deploy a database technology on an EC2 instance and the vendor license bills you based on the physical cores and underlying network socket visibility. Which EC2 Purchasing Option allows you to get visibility into them?
  - Dedicated Host






  
  
  
  
