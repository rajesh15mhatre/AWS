# Section 8: High availability and scalability: ELB and ASG
Source: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13531374#overview
## 69. High availibility and scalability
- Scalability means tha an app/system can handle greater loads by adapting
- There are two kinds of scalibility
  - vertical scalability
  - horizontal scalability (=elasticity)
- Scalbility is linked but different to High availibility
- Vertical scalability:
  - increasing size of instance from t2.micro to t2 large
  - vertical scalability is common for non-distributed systems, such as databses, RDS, ElasiCache are services that can scale vertcally 
  - There's usually a limit to how much you can vertically scale (hardware limit)
- Horizontal scalability
  - It means increasing the number of instances/ systems for your app
  - it implies distributed system
  - This is very common for web app/modern apps
  - Its easy to scale horizontally using cloud offerings like EC2
- High availability - HA
  - High availibily goes hand in hand with horizontal scaling
  - It means running your app in atleast 2 data center/AZ
  - The goal of HA is to survive a data center loss
  - The HA can be passive - for RDS Multi-AZ 
  - The HA can be active - for horizontal scaling
- HA & scalability for EC2
  - Vertical scaling - Increse instance size = scale up/down
    - From t2.nano - 0.5GB of RAM, 1 vCPU
    - To u-12tb1.metal - 12.3TB RAM, 448 vCPUs
  - HA scaling: Increase number of instances = scale out/in(low numbers)
    - Auto scaling group
    - Load balancer
  - HA : Run instances for the same app across multi AZ
    - Auto scaling Group multi AZ
    - Load balancer multi AZ
## 70 Elastic load balancing (ELB) overview
- Why to use ELB
  - Spread load across multiple downstream instacnes
  - Expose a single point of access (DNS) to your app
  - Seamless handle failuers of downstream instances
  - Do regular health checks to your instances
  - Provide SSL termination (HTTPS) for your websites
  - Enforce stickiness with cookies
  - HA across AZ
  - Separate public traffic from private traffic
  - It is a managed load balancer
    - AWS guarantees that it will be working 
    - AWS takes care of upgrades, maintainance, HA
    - AWS provides only a few configuration knobs
  - Its costs less to setup your own load balancer but it will be a lot of more efforts on your end
  - It is integrated with many AWS offerings/services
    - EC2, Ec2 auto scaling groups, Amazon ECS
    - AWS certificate managers(ACM), CloudWatch
    - Route 53, AWS WAF, AWS GLobal Accelerator
- Health cheks
  - Are crutial for load balancer
  - They enable the load balancer to know if instance it forwards traffic to are available to reply to request
  - Health check is done on a port and route
  - If the resonse in not 200(OK) then the instacne is unhealthy
- Types of load balancers
  - AWS has 4 kinds of managed Load Balancers
  - Classic Load Balancer (v1 - old generation - deprected) – 2009 – CLB
    - HTTP, HTTPS, TCP, SSL (secure TCP)
  - Application Load Balancer (v2 - new generation) – 2016 – ALB
    - HTTP, HTTPS, WebSocket
  - Network Load Balancer (v2 - new generation) – 2017 – NLB
    - TCP, TLS (secure TCP), UDP
  - Gateway Load Balancer – 2020 – GWLB
    - Operates at layer 3 (Network layer) – IP Protocol
- Overall, it is recommended to use the newer generation load balancers as they provide more features
- Some load balancers can be setup as internal (private) or external (public) ELBs
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/3a53b538-bac9-4666-ae13-174af2ddd45a)

## 71. Note about CLassic load balancer
Note: About the Classic Load Balancer (CLB)
The Classic Load Balancer is deprecated at AWS and will soon not be available in the AWS Console.
The exam also has removed any references to it, so we will not cover it in depth in the course, nor the hands-on

## 72. Application Load Balancer (ALB) 
- Application load balancers is Layer 7 (HTTP)
- Load balancing to multiple HTTP applications across machines (target groups)
- Load balancing to multiple applications on the same machine (ex: containers)
- Support for HTTP/2 and WebSocket
- Support redirects (from HTTP to HTTPS for example)
- Routing tables to different target groups:
  - Routing based on path in URL (example.com/users & example.com/posts)
  - Routing based on hostname in URL (one.example.com & other.example.com)
  - Routing based on Query String, Headers
(example.com/users?id=123&order=false)
- ALB are a great fit for micro services & container-based application (example: Docker & Amazon ECS)
- Has a port mapping feature to redirect to a dynamic port in ECS
- In comparison, we’d need multiple Classic Load Balancer per application
- Below using load balancer routing user based on user/search
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/307ec06c-0b56-4264-9b34-670f08395585)
- Application Load Balancer(v2) Target Groups
  - EC2 instances (can be managed by an Auto Scaling Group) – HTTP
  - ECS tasks (managed by ECS itself) – HTTP
  - Lambda functions – HTTP request is translated into a JSON event
  - IP Addresses – must be private IPs
  - ALB can route to multiple target groups
  - Health checks are at the target group level
- Example of query string /parameter routing; where mobile traffic moved to cloud app and desktop trafiic to on-premises
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/20de5852-3624-4033-a47e-9d92de4a9035)
- Good to Know
  - Fixed hostname (XXX.region. -lb.amazonaws.com)
  - The application servers don’t see the IP of the client directly
  - The true IP of the client is inserted in the header **X-Forwarded-For**
  - We can also get Port (X-Forwarded-Port) and proto (X-Forwarded-Proto)
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/92e5dcb1-16fb-40a5-8425-1cdb900f30f5)
## 73. ALB Hands on - part 1
- First create 2 ec2 isntances with user data script
- then create ALB from load balancing menu
- Choose ALB--> Create
- Give name `Demo` ALB-> schema internet facing and address type is IPv4 
- Create a security group to allow http traffic from anywhere into the load balancer
- Select that secutrity group
- Under listener and routing 
  - create a target group 
    - select group instaces 
    - name it
    - protocal http on port 80
    - select ec2 instacnes on next page
- Now select created target group on port 80 and http protocol
- Click on create balancer button
- Copy DSN link and test it
- keep refreshing so you can see different EC2 IPs as balancer route request to differnt instance

## Application load balancer Hands on Part-2
- In order to make an app only be accessible through Load balancer
  - Delete inbound rule of security group which allows HTTP traffic to EC2 instance
  - Create new inboud rule  to allow HTTP traffic only from Load balance by selecting:
  -  Source: custom and IP of loadbalancer security group
 
 Cutomized Rout rule:
 - Goto Demo ALB load balancer 
 - Goto listen --> Here we can set differnt rule based on differnt situation - check drop down and explore
 
 ## 75. Network Load balancer
- Network load balancers (Layer 4) allow to:
- Forward TCP & UDP traffic to your instances
- Can handle millions of request per seconds
- Less latency ~100 ms (vs 400 ms for ALB)
- NLB has one static IP per AZ, and supports assigning Elastic IP (helpful for whitelisting specific IP)
- NLB are used for extreme performance, TCP or UDP traffic
- Not included in the AWS free tier
- NLB Target groups
  - EC2 instances
  - IP address - must be private IPs - for on premises sever
  - ALB: We can use NLB on top of ALB to leverage its route listening rules
  - **Health Checks supports the TCP, HTTP and HTTPS protocols**

## 76. NLB Hands on
- Create load balancer-> select NLB
- select internet facing, ipv4
- select AZs
  - in each AZ we can use AWS IP or Elastic IP
- Create target group --> name it
  - Select protocol TCP; port 80
  - Health check -> according to app --> HTTP
  - select path /
  - interval 5 
  - click next 
  - create target group
- Select target group create load balancer
- add a rule in security group to allow HTTP from anywherer

- for clean up delete 
  - load balancer
  - Target groups
  - Inbound rule delete which allows http from anywhere 

## 77. Gateway load balancer
- Its allows us to validate traffic before visting instance. So first tarfiic goto to GLB then to target group where it gets validated then to EC2 via GLB 
- Deploy, scale, and manage a fleet of 3rd party network virtual app in AWS
  - Ex: Firewalls, Intrusion Detection and prevention Systems, Deep Packer Inspection Systems, payload manipulation
- Operates at Layer 3 (network Layer) - IP Packets
- Combines the following fuctions :
  - **Transparent Network Gateway**: Single entry/exit for all traffic
  - **Load Balancer**: distributes traffic to your virtual app
- **Uses the GENEVE protocol on port 6081**
- Target groups
  - EC2
  - IP Address
## 78. ELB Sticky Sessions (Session Affinity)
- means same user request goes to same EC2 instance viw LB(load balancer)
- It is possible to implement stickiness so that the same client is always redirected to the same
instance behind a load balancer
- This works for **Classic Load Balancers & Application Load Balancers and NLB**
• The “cookie” used for stickiness has an expiration date which you control
• Use case: make sure the user doesn’t lose user's session data
• Enabling stickiness may bring imbalance to the load over the backend EC2 instances
- **Note: NLB also works without cookies**
- Cookie Names
  - Application-based Cookies
    - Custom cookie
      - Generated by the target
      - Can include any custom attributes required by the application
      - Cookie name must be specified individually for each target group
      - Don’t use **AWSALB, AWSALBAPP, or AWSALBTG** (reserved for use by the ELB)
    - Application cookie
      - Generated by the load balancer
      - Cookie name is AWSALBAPP
  - Duration-based Cookies
    - Cookie generated by the load balancer
    - Cookie name is **AWSALB** for ALB, **AWSELB** for CLB
- Hands ON
  - goto Target groups
  - action-> edit attributes
  - Check stickiness checkbox
    - Select suitable cookie
      - load balancer
      - App-based
        - give duration and name
    - Save changes
    - and check web app you will get same ip evern after multiple refresh
    -  you can check cookie info in chrome deleveloper tools under network tab
## 79. EL- Cross zone balancing  
- Cross zone balancing distribute traffic evently across all regisered instances in all AZ
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/14227221-03a4-4e8d-b144-18f7baad7e57)
- ALB: 
  - Cross zone is eanabled by default
  - NO changes for inter AZ data 
- NLB & GLB
  - Disabled by default
  - You pay charges ($) for inter AZ data if enabled
- Classic Load Balancer (Depricated)
  - Through Console => Enabled by default
  - Through CLI / API => Disabled by default
  - No charges for inter AZ data if enabled
- Hands on
  - NLB:  Goto NLB scrolldown --> attributes
    - Edit and turn on cross zone balancing
  - GLB:  Goto NLB scrolldown --> attributes
    - Edit and turn on cross zone balancing
  - ALB: Goto NLB scrolldown --> attributes
    - Goto target group --> attributes 
    - Edit -> turn off or on 
## 80. ELB - SSL certificate
- SSL/TLS Basics
  - An SSL Certificate allows traffic between your clients and your load balancer
  to be encrypted in transit (in-flight encryption)
  - SSL refers to Secure Sockets Layer, used to encrypt connections
  - TLS refers to Transport Layer Security, which is a newer version
  - Nowadays, TLS certificates are mainly used, but people still refer as SSL
  - Public SSL certificates are issued by Certificate Authorities (CA)
  - Comodo, Symantec, GoDaddy, GlobalSign, Digicert, Letsencrypt, etc…
  - SSL certificates have an expiration date (you set) and must be renewed
- Load Balancer - SSL Certificate
 ![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/d7ff05b3-f584-4782-a653-302c347b9d10)
  - The load balancer uses an X.509 certificate (SSL/TLS server certificate)
  - You can manage certificates using ACM (AWS Certificate Manager)
-  You can create upload your own certificates alternatively
  - HTTPS listener:
    - You must specify a default certificate
    - You can add an optional list of certs to support multiple domains
    - **Clients can use SNI (Server Name Indication) to specify the hostname they reach**
    - Ability to specify a security policy to support older versions of SSL / TLS (legacy clients)
- SSL – Server Name Indication (SNI)
  - **SNI solves the problem of loading multiple SSL
certificates onto one web server (to serve
multiple websites)**
  - It’s a “newer” protocol, and requires the client
to indicate the hostname of the target server
in the initial SSL handshake
  - The server will then find the correct
certificate, or return the default one
  -Note:
    - Only works for ALB & NLB (newer
generation), CloudFront
    - Does not work for CLB (older gen)
- Elastic Load Balancers – SSL Certificates
  - Classic Load Balancer (v1)
    - Support only one SSL certificate
    - Must use multiple CLB for multiple hostname with multiple SSL certificates
  - Application Load Balancer (v2)
    - Supports multiple listeners with multiple SSL certificates
    - Uses Server Name Indication (SNI) to make it work
  - Network Load Balancer (v2)
    - Supports multiple listeners with multiple SSL certificates
    - Uses Server Name Indication (SNI) to make it work

## 81. ELB SSL certificate Hands on
- ALB 
  - goto ALB -> listner -> add
  - HTTPS-443
  - select target group 
  - Select Security policy 
  - SLL/TLS certificate
    - Select exting certificate or import by pasting certificate 
- NLB 
  - goto NLB -> listner -> add
  - TLS-443
  - select target group 
  - Select Security policy 
  - SLL/TLS certificate
    - Select exting certificate or import by pasting certificate 
    - Select application layer protocol negotionation

## 82. ELB Connection draining
- **Feature naming**
  - Connection Draining – for CLB
  - Deregistration Delay – for ALB & NLB
- Time to complete “in-flight requests” while the instance is de-registering or unhealthy
- Stops sending new requests to the EC2
instance which is de-registering/draining
- Between 1 to 3600 seconds (default: 300
seconds)
- Can be disabled (set value to 0)
- Set to a low value if your requests are short

## 83. Auto Scaling Group
- In real-life, the load on your websites and application can change
- In the cloud, you can create and get rid of servers very quickly
- The goal of an Auto Scaling Group (ASG) is to:
  - Scale out (add EC2 instances) to match an increased load
  - Scale in (remove EC2 instances) to match a decreased load
  - Ensure we have a minimum and a maximum number of machines running
  - Automatically Register new instances to a load balancer
- ASGs have the following attributes
  - A **Launch Template**  (Older "Launch Configuraitons" are deprecated)
    - AMI + Instance Type
    - EC2 User Data
    - EBS Volumes
    - Security Groups
    - SSH Key Pair
    - IAM Roles for your EC2 Instance
    - Network + Subnet Information
    - Load Balancer Information
  - Min Size / Max Size / Initial Capacity
  - Scaling Policies
- Auto Scaling - **CloudWatch alarms & Scaling** 
  - It is possible to scale an ASG based on CloudWatch alarms
  - An Alarm monitors a metric (such as **Average CPU** Or a **custom metric**)
  - Metrics are computed for the overall ASG instances
  - Based on the alarm:
     - We can create scale-out policies (increase the number of instances)
    - We can create scale-in policies (decrease the number of instances)
##  84. Auto scaling group handson 
- Fisrt terminate all exiting Ec2 instances
- In EC2 navigation -> select auto scaling group
- Name it -> create a lunch template\ similar to EC2 creaete don't select subnet
- Use template
- select AZ
- select load balancer which we cresated earlier ALB
- Under health check : select ELB option so load banlanver fins any EC2 is unheaklthy then it will terminate it 
- Select your group size 
- SCalaing poklicies none
- create auto scaling group
## 85. Auto Scaling Groups - scaling policies
- Auto Scaling New Rules
  - It is now possible to define ”better” auto scaling rules that are directly managed by EC2
    - Target Average CPU Usage
    - Number of requests on the ELB per instance
    - Average Network In
    - Average Network Out
    - These rules are easier to set up and can make more sense
- Auto Scaling Custom Metric
  - We can auto scale based on a custom metric (ex: number of connected users)
    - 1. Send custom metric from application on EC2 to CloudWatch (PutMetric API)
    - 2. Create CloudWatch alarm to react to low / high values
    - 3. Use the CloudWatch alarm as the scaling policy for ASG
- ASG Brain Dump
  - Scaling policies can be on CPU, Network… and can even be on custom metrics or based on a schedule (if you know your visitors patterns)
  - ASGs use Launch configurations or Launch Templates (newer)
  - To update an ASG, you must provide a new launch configuration / launch template
  - IAM roles attached to an ASG will get assigned to EC2 instances
  -  ASG are free. You pay for the underlying resources being launched
  - Having instances under an ASG means that if they get terminated for whatever reason, the ASG will automatically create new ones as a replacement. Extra safety!
  - ASG can terminate instances marked as unhealthy by an LB (and hence replace them)
- Auto Scaling Groups – Dynamic Scaling Policies
  - Target Tracking Scaling
    - Most simple and easy to set-up
    - Example: I want the average ASG CPU to stay at around 40%
  - Simple / Step Scaling
    - When a CloudWatch alarm is triggered (example CPU > 70%), then add 2 units
    - When a CloudWatch alarm is triggered (example CPU < 30%), then remove 1
  - Scheduled Actions
    - Anticipate a scaling based on known usage patterns
    - Example: increase the min capacity to 10 at 5 pm on Fridays
  - Predictive scaling: continuously forecast load and schedule scaling ahead
- Good metrics to scale on
  - CPUUtilization: Average CPU utilization across your instances
  - RequestCountPerTarget: to make sure the number of requests per EC2 instances is stable
  - Average Network In / Out (if you’re application is network bound)
  - Any custom metric (that you push using CloudWatch)
- Auto Scaling Groups - Scaling Cooldowns
  - After a scaling activity happens, you are in the **cooldown period (default 300 seconds)**
  - During the cooldown period, the ASG will not launch or terminate additional instances (to allow for metrics to stabilize)
  - Advice: Use a ready-to-use AMI to reduce configuration time in order to be serving request fasters and reduce the cooldown period
## 86. ASG Scaling policies Hands on
- Under ASG --> automatic scaling
  - Schedule policy- set desired pollicies 
  - Predictive scaling - turn on and set metrics
  - Dyanamic scaling policy 
    - select type
    - select cloudwater alarm
    - add capacity
    - You can set thrsholf like cpu utilisation under tagert policy
      - set taget to 50% 
      - Install stress amazon linux uitility (google search  in increase cpu utilization to test ASG
        - _$ sudo amazon-linux-extras install epel -y_
        - _$ sudo yum install stress -y_  
      - increase cpu utilization
        - _$ stress -c 4_ 
      - Wait till cpu utilization goes up 
      - check activity ec2 instances will be created
      - goto cloudwatch - alarams  - check alarms
      - stop stress process / reboot ec2
      - ASG will scale in
  - Delete the policy  
## Quiz
- Scaling an EC2 instance from r4.large to r4.4xlarge is called 
  - vertical scalability
- Running an application on an Auto Scaling Group that scales the number of EC2 instances in and out is called 
  - Horizontal Scalability
- Elastic Load Balancers provide a 
  - static DNS name we can use in our app
    - Only Network Load Balancer provides both static DNS name and static IP. While, Application Load Balancer provides a static DNS name but it does NOT provide a static IP. The reason being that AWS wants your Elastic Load Balancer to be accessible using a static endpoint, even if the underlying infrastructure that AWS manages changes.
- You are running a website on 10 EC2 instances fronted by an Elastic Load Balancer. Your users are complaining about the fact that the website always asks them to re-authenticate when they are moving between website pages. You are puzzled because it's working just fine on your machine and in the Dev environment with 1 EC2 instance. What could be the reason?
  - The ELB does not have Sticky Session enabled
    - ELB Sticky Session feature ensures traffic for the same client is always redirected to the same target (e.g., EC2 instance). This helps that the client does not lose his session data.
- You are using an Application Load Balancer to distribute traffic to your website hosted on EC2 instances. It turns out that your website only sees traffic coming from private IPv4 addresses which are in fact your Application Load Balancer's IP addresses. What should you do to get the IP address of clients connected to your website?
  - Modify your website's backend to get the client IP address from the **X-forwarded-For** header
    - When using an Application Load Balancer to distribute traffic to your EC2 instances, the IP address you'll receive requests from will be the ALB's private IP addresses. To get the client's IP address, ALB adds an additional header called "X-Forwarded-For" contains the client's IP address.
- You hosted an application on a set of EC2 instances fronted by an Elastic Load Balancer. A week later, users begin complaining that sometimes the application just doesn't work. You investigate the issue and found that some EC2 instances crash from time to time. What should you do to protect users from connecting to the EC2 instances that are crashing?
  - Enable ELB Health Checks
    - When you enable ELB Health Checks, your ELB won't send traffic to unhealthy (crashed) EC2 instances.
- You are working as a Solutions Architect for a company and you are required to design an architecture for a high-performance, low-latency application that will receive millions of requests per second. Which type of Elastic Load Balancer should you choose?
  - Network Load Balancer
    - Network Load Balancer provides the highest performance and lowest latency if your application needs it.
- Application Load Balancers support the following protocols, EXCEPT:
  - TCP
    - Application Load Balancers support HTTP, HTTPS and WebSocket
- Application Load Balancers can route traffic to different Target Groups based on the following, EXCEPT:
  - Client's Location (Geography) 
    - ALBs can route traffic to different Target Groups based on URL Path, Hostname, HTTP Headers, and Query Strings.
- Registered targets in a Target Groups for an Application Load Balancer can be one of the following, EXCEPT:
  - EC2, Private IP, Lambda except **NLB**
- For compliance purposes, you would like to expose a fixed static IP address to your end-users so that they can write firewall rules that will be stable and approved by regulators. What type of Elastic Load Balancer would you choose?
  - NLB
    - Network Load Balancer has one static IP address per AZ and you can attach an Elastic IP address to it. Application Load Balancers and Classic Load Balancers have a static DNS name.
- 

- 13. You have a Network Load Balancer that distributes traffic across a set of EC2 instances in us-east-1. You have 2 EC2 instances in us-east-1b AZ and 5 EC2 instances in us-east-1e AZ. You have noticed that the CPU utilization is higher in the EC2 instances in us-east-1b AZ. After more investigation, you noticed that the traffic is equally distributed across the two AZs. How would you solve this problem?
  - Enable cross zone balancing
    - When Cross-Zone Load Balancing is enabled, ELB distributes traffic evenly across all registered EC2 instances in all AZs.
- Which feature in both Application Load Balancers and Network Load Balancers allows you to load multiple SSL certificates on one listener?
  - Server Name Indication (SNI)
- 15. You have an Application Load Balancer that is configured to redirect traffic to 3 Target Groups based on the following hostnames: users.example.com, api.external.example.com, and checkout.example.com. You would like to configure HTTPS for each of these hostnames. How do you configure the ALB to make this work?
  - Use SNI 
    - Server Name Indication (SNI) allows you to expose multiple HTTPS applications each with its own SSL certificate on the same listener. Read more here: https://aws.amazon.com/blogs/aws/new-application-load-balancer-sni/
- 16 You have an application hosted on a set of EC2 instances managed by an Auto Scaling Group that you configured both desired and maximum capacity to 3. Also, you have created a CloudWatch Alarm that is configured to scale out your ASG when CPU Utilization reaches 60%. Your application suddenly received huge traffic and is now running at 80% CPU Utilization. What will happen?
  - Nothing
    - The Auto Scaling Group can't go over the maximum capacity (you configured) during scale-out events.
- 17.You have an Auto Scaling Group fronted by an Application Load Balancer. You have configured the ASG to use ALB Health Checks, then one EC2 instance has just been reported unhealthy. What will happen to the EC2 instance?
  - The ASG will terminate the EC2 instance
    - You can configure the Auto Scaling Group to determine the EC2 instances' health based on Application Load Balancer Health Checks instead of EC2 Status Checks (default). When an EC2 instance fails the ALB Health Checks, it is marked unhealthy and will be terminated while the ASG launches a new EC2 instance.
- 18. Your boss asked you to scale your Auto Scaling Group based on the number of requests per minute your application makes to your database. What should you do?
  - Create a cloudwatch custom metric the create a cloudWatch alarm on this metric to scale your ASG
    - There's no CloudWatch Metric for "requests per minute" for backend-to-database connections. You need to create a CloudWatch Custom Metric, then create a CloudWatch Alarm.
- 19. An application is deployed with an Application Load Balancer and an Auto Scaling Group. Currently, you manually scale the ASG and you would like to define a Scaling Policy that will ensure the average number of connections to your EC2 instances is around 1000. Which Scaling Policy should you use?
  - Target scaling policy
- 20. You have an ASG and a Network Load Balancer. The application on your ASG supports the HTTP protocol and is integrated with the Load Balancer health checks. You are currently using the TCP health checks. You would like to migrate to using HTTP health checks, what do you do?
  - Migrate the health check to HTTP
    - the NLB supports HTTP health checks as well as TCP and HTTPS
- 21. You have a website hosted in EC2 instances in an Auto Scaling Group fronted by an Application Load Balancer. Currently, the website is served over HTTP, and you have been tasked to configure it to use HTTPS. You have created a certificate in ACM and attached it to the Application Load Balancer. What you can do to force users to access the website using HTTPS instead of HTTP?
  - Configure the application load balancer to redirect HTTP to HTTPS
# Retry this quiz

