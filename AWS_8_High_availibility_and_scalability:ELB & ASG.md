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
 













