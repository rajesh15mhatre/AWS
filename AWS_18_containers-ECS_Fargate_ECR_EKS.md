# Section 18 : Containers in AWS- ECS, Fargate, ECR & EKS
Link: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26099482#overview

## 200. Docker instroduction
- What is Docker?
  - Docker is a software development platform to deploy apps
  - Apps are packaged in containers that can be run on any OS
  - Apps run the same, regardless of where they’re run
    - Any machine
    - No compatibility issues
    - Predictable behavior
    - Less work
    - Easier to maintain and deploy
    - Works with any language, any OS, any technology
  - Use cases: microservices architecture, lift-and-shift apps from onpremises
to the AWS cloud, …
- Where are Docker images stored?
  - Docker images are stored in Docker Repositories
  - Docker Hub (https://hub.docker.com)
    - Public repository
    - Find base images for many technologies or OS (e.g., Ubuntu, MySQL, …)
  - Amazon ECR (Amazon Elastic Container Registry)
    - Private repository
    - Public repository (Amazon ECR Public Gallery https://gallery.ecr.aws)
- Docker vs. Virtual Machines
  - Docker is ”sort of” a virtualization technology, but not exactly
  - Resources are shared with the host => many containers on one server
- Docker Containers Management on AWS
  - Amazon Elastic Container Service (Amazon ECS)
    - Amazon’s own container platform
  - Amazon Elastic Kubernetes Service (Amazon EKS)
    - Amazon’s managed Kubernetes (open source)
  - AWS Fargate
    - Amazon’s own Serverless container platform
    - Works with ECS and with EKS
  - Amazon ECR:
    - Store container images
 
## 201. Amazon ECS
- Amazon ECS - EC2 Launch Type
  - ECS = Elastic Container Service
  - Launch Docker containers on AWS = Launch ECS Tasks on ECS Clusters
  - **EC2 Launch Type: you must provision & maintain the infrastructure (the EC2 instances)**
  - Each EC2 Instance must run the ECS Agent to register in the ECS Cluster
  - AWS takes care of starting/stopping containers
- Amazon ECS – Fargate Launch Type
  - Launch Docker containers on AWS
  - You do not provision the infrastructure (no EC2 instances to manage) it’s all Serverless!
  - You just create task definitions
  - AWS just runs ECS Tasks for you based on the CPU / RAM you need
  - To scale, just increase the number of tasks. Simple - no more EC2 instances
- Amazon ECS – IAM Roles for ECS
  - **EC2 Instance Profile (EC2 Launch Type only):**
    - Used by the ECS agent
    - Makes API calls to ECS service
    - Send container logs to CloudWatch Logs
    - Pull Docker image from ECR
    - Reference sensitive data in Secrets Manager or SSM Parameter Store
  - **ECS Task Role** ( for both EC2 and Fargate launch types) :
    - Allows each task to have a specific role
    - Use different roles for the different ECS Services you run
    - Task Role is defined in the **task definition**
- Amazon ECS – Load Balancer Integrations
  - **Application Load Balancer** supported and works for most use cases
  - **Network Load Balancer** recommended only for high throughput / high performance use cases, or to pair it with AWS Private Link
  - **Elastic Load Balancer** supported but not recommended (no advanced features – no Fargate)
- Amazon ECS – Data Volumes (EFS)
  - Mount EFS file systems onto ECS tasks
  - Works for both **EC2** and **Fargate** launch types
  - Tasks running in any AZ will share the same data in the EFS file system
  - **Fargate + EFS = Serverless**
  - Use cases: persistent multi-AZ shared storage for
your containers
  - Note:
    - Amazon S3 cannot be mounted as a file system

## 202. just a note about UI changes

## 203. Creating ECS cluster - Hands-on
- Goto ECS - create cluster and name it **DemoCluster**
- Select default VPC
- Fargate launched type is by default selected - Can not unselect
- Select ECS instance launch type as well (optional)
  - A new ASG will be created
  - select OS, EC2 instance type-t2micro, min 0 and max 5 instances, shh keys pair (optional)
The external instance option is to link your on-prem servers
- Create
- Edit ec2 min capacity to 1 by editing ASG settings
  - 1 EC2 instance will be created
- Let's create a service in the next lecture

## 204. Creating ECS service - Hands-on
- Create a service
  - Goto ECS -> task definition - create new
  - name it nginxdemo-hello ( which is an image from docker hub)
  - in container 1 provide
    - name : ngincxdemo-hello
    - Image URI : nginxdemo/hello
    - Port : 80 - Next
    - Select app env ( Fargate/ EC2) we select fargate
    - OS - linux , cpu 5 vcpu, mem 1GB
    - We can add **task role** if task plan to interact with any AWs service
    - Next - Create
  - Create 2 security groups
    - EC2- Security groups -
      - For ECS: create - name it alb-ecs-sg
       - create inbound rules: TCP port 8-0 anywhere from ipv6 and ipv6
     - SG for ECS task
       - name it - ngix-demo-sg
       - inbound ALL TCP, source: custom - select above security group alb-ecs-sg
       - Create
- Run a service
  -   Goto clusters - demoCulster - click on **deploy** button to create and deploy the service
  -   Type: service
  -   Family nginxdemo-hello
  -   service name - nginxdemos
  -   desired task = 1
  -   select the application load balancer
     -  create new , nae it DemoALBForECS
     -  listening 80, http
     -  Target nginx-ecs protocol http
     -  Health check path = /
     -  grace period = 20
   -  select the existing nginx-demo-sg security group
   -  CLick deploy
 
## 205. ECS auto-scaling
- ECS Service Auto Scaling
  - Automatically increase/decrease the desired number of **ECS tasks**
  - Amazon ECS Auto Scaling uses AWS **Application Auto Scaling**
    - ECS Service Average CPU Utilization
    - ECS Service Average Memory Utilization - Scale on RAM
    - ALB Request Count Per Target – metric coming from the ALB
  - **Target Tracking** – scale based on target value for a specific CloudWatch metric
  - **Step Scaling** – scale based on a specified CloudWatch Alarm
  - **Scheduled Scaling** – scale based on a specified date/time (predictable changes)
  - ECS Service Auto Scaling (task level) is not equals to   EC2 Auto Scaling (EC2 instance level)
    - It only scales up/down tasks on same EC2 if the launch type is EC2
  - Fargate Auto Scaling is much easier to setup (because **Serverless**)
- EC2 Launch Type – Auto Scaling EC2 Instances
  - Accommodate ECS Service Scaling by adding underlying EC2 Instances
  - **Auto Scaling Group Scaling**
    - Scale your ASG based on CPU Utilization
    - Add EC2 instances over time
  - **ECS Cluster Capacity Provider (preferred option)**
    - Used to automatically provision and scale the infrastructure for your ECS Tasks
    - Capacity Provider paired with an Auto Scaling Group
    - Add EC2 Instances when you’re missing capacity (CPU, RAM…) ex. 80% capacity is full

## 206. Amazon ECS - solution architecture
1. ECS serverless task invoked by event bridge
  - User uploads object in S3 bucket
  - S3 sent event to Event bridge
  - Event bridge has a rule to run ECS task
  - An ECS task on **Fargate** will be created and it will have a role created to access S3 and DynamoDB
  - Task retrieves an object from s3 process and stores into DynamoDB
2. ECS serverless task invoked by Event Bridge Schedule
  - Event bridge run ECS task in Farget cluster every hour
  - Task will have a role created to access s3 for batch processing

3. ECS - SQS Queue Example
- A service on ECS with 2 tasks which pools messages from the SQS queue
- Auto-scaling enabled on SQS message count to scale tasks4.

4. ECS - Intercept Stopped Task using EventBridge
- ECS task sents status as event to event bridge like (stat/stopped) in JSON
- Event bridge triggers SNS topic which sends email to user about the status of the ECS task

## 208. Amazon ECR 
