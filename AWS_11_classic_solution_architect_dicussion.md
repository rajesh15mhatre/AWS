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
















