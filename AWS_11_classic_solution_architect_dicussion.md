# Section 11: Solution Architect discussion
Link: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13727286#overview

## 120. Solution Architecture Discuttion Overview
- Will cover case studies

## 121. WhatistheTime.com
- Stateless WebApp - whatIsTheTime.com
  - WhatIsTheTime.com allows people to know what time it is
  - We don’t need a database
  - We want to start small and can accept downtime
  - We want to fully scale vertically and horizontally, no downtime
  - Let’s go through the Solutions Architect journey for this app
- We discussed:
  - Public vs Private IP and EC2 instances
  - Elastic IP vs Route 53 vs Load Balancers
  - Route 53 TTL, A records and Alias Records
  - Maintaining EC2 instances manually vs Auto Scaling Groups
  - Multi AZ to survive disasters
  - ELB Health Checks
  - Security Group Rules
  - Reservation of capacity for costing savings when possible
  - We’re considering 5 pillars for a well architected application: costs, performance, reliability, security, operational excellence
 
## 122. MyCloths.com
- Stateful webapp:
  - MyClothes.com allows people to buy clothes online.
  - There’s a shopping cart
  - Our website is having hundreds of users at the same time
  - We need to scale, maintain horizontal scalability and keep our web application as stateless as possible
  - Users should not lose their shopping cart
  - Users should have their details (address, etc) in a database
  - Let’s see how we can proceed!
- We discussed:
  - 3-tier architectures for web applications
  - ELB sticky sessions
  - Web clients for storing cookies and making our web app stateless
  - ElastiCache
    - For storing sessions (alternative: DynamoDB)
    - For caching data from RDS
    - Multi AZ
  - RDS
    - For storing user data
    - Read replicas for scaling reads
    - Multi AZ for disaster recovery
    - Tight Security with security groups referencing each other
## 123. Sateful web app - wordpress.com
- Overview
  - We are trying to create a fully scalable WordPress website
  - We want that website to access and correctly display picture uploads
  - Our user data, and the blog content should be stored in a MySQL database.
  - Let’s see how we can achieve this
- Solution discussed
  - Aurora Database to have easy Multi-AZ and Read-Replicas
  - Storing data in EBS (single instance application)
  - Vs Storing data in EFS (distributed application)
## 124. Instantiating Application
- How to speed up application startup
- When launching a full stack (EC2, EBS, RDS), it can take time to:
  - Install applications
  - Insert initial (or recovery) data
  - Configure everything
  - Launch the application
- We can take advantage of the cloud to speed that up!
- EC2 Instances:
  - **Use a Golden AMI**: Install your applications, OS dependencies etc.. beforehand
and launch your EC2 instance from the Golden AMI
  - **Bootstrap using User Data**: For dynamic configuration, use User Data scripts
  - **Hybrid**: mix Golden AMI and User Data (Elastic Beanstalk)
- RDS Databases:
  - Restore from a snapshot: the database will have schemas and data ready!
- EBS Volumes:
  - Restore from a snapshot: the disk will already be formatted and have data!

## 125. Elastic Beanstalk - Overview
- Developer problems on AW
  - Managing infrastructure
  - Deploying Code
  - Configuring all the databases, load balancers, etc
  - Scaling concerns
  - Most web apps have the same architecture (ALB + ASG)
  - All the developers want is for their code to run!
  - Possibly, consistently across different applications and environments
- Elastic Beanstalk – Overview
  - Elastic Beanstalk is a developer centric view of deploying an application on AWS
  - It uses all the component’s we’ve seen before: EC2, ASG, ELB, RDS, …
 - Managed service
    - Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance configuration, …
    - Just the application code is the responsibility of the developer
  - We still have full control over the configuration
  - Beanstalk is free but you pay for the underlying instances
- Elastic Beanstalk – Components
  -  Application: collection of Elastic Beanstalk components (environments, versions, configurations, …)
  - Application Version: an iteration of your application code
  - Environment
    - Collection of AWS resources running an application version (only one application version at a time)
    - Tiers: Web Server Environment Tier & Worker Environment Tier
    - You can create multiple environments (dev, test, prod, …)
- Worker Tier
  - Scale based on the number of SQS messages
  - Can push messages to SQS queue from another Web Server Tier

## 126. Beanstalk Handson
- Beanstalk - create application
- Select webserver/worker env
- Give app name
- provide env name
- domain name auto generated
- select palthform- node.js (select any programming) and version 
- select app code - sample code
- present - single istance -next
- Create a service role for elasticBeanStalk
- IAM - Role - Service - EC2 - search  beanstalk and select
  - beanstalkWebTier and BeanstalkWorkerTier next - given role name - create role
- provide role name - skip to review
- Submit
- after this behinfd the scene under **couldformation** resource stack will be created
- uner template tab we can view our app designer architecture template
- Under beanStak once status is shown as ok , click on a app domain link to test the app
- We can click  upload a new version  to create new version of env
- Under my applicaiton by using create new env button we can creaete new env

## Quiz
1. Your website TriangleSunglasses.com is hosted on a fleet of EC2 instances managed by an Auto Scaling Group and fronted by an Application Load Balancer. Your ASG has been configured to scale on-demand based on the traffic going to your website. To reduce costs, you have configured the ASG to scale based on the traffic going through the ALB. To make the solution highly available, you have updated your ASG and set the minimum capacity to 2. How can you further reduce the costs while respecting the requirements?
  - Remove the ALB and use an Elastiv IP instead
  - **Reserve teo EC2 instances** (This is the way to save further costs as we will run 2 EC2 instances no matter what.)
  - Reduce the minimum capacity to 1
  - Reduce the minimum capacity to 0

2. Which of the following will **NOT** help us while designing a **STATELESS** application tier?
  - Store session data in RDS
  - Store session data in ElastiCache
  - Store session data in the client HTTP cookies
  - **Store session data on EBS volume** (EBS volumes are created in a specific AZ and can only be attached to one EC2 instance at a time.)

3. You want to install software updates on 100s of Linux EC2 instances that you manage. You want to store these updates on shared storage which should be dynamically loaded on the EC2 instances and shouldn't require heavy operations. What do you suggest?
- Store the software updates on EBS and sync them using dat replication software from one master in each AZ
- **Store the software updates on EFS and mount EFS as a network drive at start up** (EFS is a network file system (NFS) that allows you to mount the same file system to 100s of EC2 instances. Storing software updates on an EFS allows each EC2 instance to access them.)
- Package the software update as an EBS snapshot and create EBS volume for each new software update
- Store the software updates in amazon RDS

4. As a Solutions Architect, you're planning to migrate a complex ERP software suite to AWS Cloud. You're planning to host the software on a set of Linux EC2 instances managed by an Auto Scaling Group. The software traditionally takes over an hour to set up on a Linux machine. How do you recommend you speed up the installation process when there's a scale-out event?
  - **Use a Golden AMI** (Golden AMI is an image that contains all your software installed and configured so that future EC2 instances can boot up quickly from that AMI.)
  - Bootstrap using EC2 User data
  - store the applocation in Amazon RDS
  - Retrive the app setup files from EFS
    
5. You're developing an application and would like to deploy it to Elastic Beanstalk with minimal cost. You should run it in ..................
   - Single instance mode (The question mentions that you're still in the development stage and you want to reduce costs. Single Instance Mode will create one EC2 instance and one Elastic IP.)
   - High Availability Mode

6. You're deploying your application to an Elastic Beanstalk environment but you notice that the deployment process is painfully slow. After reviewing the logs, you found that your dependencies are resolved on each EC2 instance each time you deploy. How can you speed up the deployment process with minimal impact?  
  - Place some dependancies in your code\
  - Place the dependencies in Amazon EFS
  - **Create a golden AMI that contains the dependencies and use the image to launch the EC2 instances** (Golden AMI is an image that contains all your software, dependencies, and configurations, so that future EC2 instances can boot up quickly from that AMI.)









