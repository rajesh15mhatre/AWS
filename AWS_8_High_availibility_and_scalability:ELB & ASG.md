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
  - vertical scalability is common for on distributed systems, such as databses,
  - RDS, ElasiCache are services that can scale vertcally 
  - There's usually a limit to how much you can vertically scale (hardware limit)
- Horizontal scalability
  - It means increasing the number of instances/ systems for your app
  - it implies distributed system
  - This is very common fot web app/modern apps
  - Its easy to scale horizontally using cloud offerings like EC2
- High availability - HA
  - HIgh availibily goes hand in hand with horizontal scaling
  - It means running your app in atleast 2 data center /AZ
  - the goal of HA is to survive a data center loss
  - The HA can be passive - for RDS Multi AZ 
  - The HA can be active - for horizontal scaling
- HA & scalability for EC2
  - Vertical scaling - INcrese instance size = scale up/down
    - From t2.nano - 0.5GB of RAM, 1 vCPU
    - TO u-12tb1.metal - 12.3TB RAM, 448 vCPUs
  - HA scaling: Increase number of instances = scale out/in(low number)
    - Auto scaling group
    - Load balancer
  - HA : Run instances for the same app across multi AZ
    - Auto scaling Group multi AZ
    - Load balncer multi AZ
## 70 Elastic load balancing (ELB) overview
- Why to use ELB
  - Spread load across multiple downstream instacnes
  - Expose a single point of access (DNS) to your app
  - Seamless handle failuers of downstream instances
  - Do regular health checks to your instances
  - Provide SSL termination (HTTPS) for your websites
  - Enforce stickiness with cookies
  - HA across AZ
  - Separate public trafic from private traffic
  - It is a managed load balancer
    - AWS guarantees that it will be working 
    - AWS takes care of upgrades, maintainance, HA
    - AWS provides only a few configuration knobs
  - Its costs less to setup your own load balancer but it will be a lot of more efforts on your end
  - It is integrated with many AWS offerings/services
    - EC2m Ec2 auto scaling groups, Amazon ECS
    - AWS certificate managers(ACM), CloudWatch
    - Route 53, AWS WAF, AWS GLobal Accelerator
- HEalth cheks
  - Are crutial for load balancer
  - They enable the load blaancer to know if instance it forwards traffic to are available to reply to request
  - Health check is done on a port and route
  - If the resonse in not 200(OK) then the instacne is unhealthy
- Types of load balancers
  - AWS has 4 kinds of managed Load Balancers
  - Classic Load Balancer (v1 - old generation) – 2009 – CLB
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
- Example of qury string /parameter routing; where mobile traffic moved to cloud app and desktop trafiic to on-premises
![image](https://github.com/rajesh15mhatre/AWS/assets/15013611/20de5852-3624-4033-a47e-9d92de4a9035)
- Good to Know
  - Fixed hostname (XXX.region. -lb.amazonaws.com)
  - The application servers don’t see the IP of the client directly
  - The true IP of the client is inserted in the header **X-Forwarded-For**
  - We can also get Port (X-Forwarded-Port) and proto (X-Forwarded-Proto)




















