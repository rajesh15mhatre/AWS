# Section 10: Route 53
https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/28384280#overview
## 101. What is DNS
- Overview
  - Domain Name System which translates the human-friendly hostnames into the machine IP addresses
  - www.google.com => 172.217.18.36
  - DNS is the backbone of the Internet
  - DNS uses the hierarchical naming structure
- DNS Terminologies
  - Domain Registrar: Amazon Route 53, GoDaddy, …
- - DNS Records: A, AAAA, CNAME, NS, …
  - Zone File: contains DNS records
  - Name Server: resolves DNS queries (Authoritative or Non-Authoritative)
  - Top Level Domain (TLD): .com, .us, .in, .gov, .org, …
  - Second Level Domain (SLD): amazon.com, google.com, …
  - http(protocol)://api(domain name).www.(subdomain).example(SLD).(TLD)com(ROOT) =  fully qualified domain name
- How DNS works
  - Client request example.com to local DNS server
  - Local DNS seeach url in root DNS server
  - if not found Then searches TLD server
  - als further searches SLD DNS server
  - Once found result LOcal DNS servers save the result in the cache
## 102 Route 53 - overview
- Overview
  - A highly available, scalable, fully managed and Authoritative DNS
  - Authoritative = the customer (you) can update the DNS records
  - Route 53 is also a Domain Registrar
  - Ability to check the health of your resources
  - The only AWS service which provides 100% availability SLA
  - Why Route 53? 53 is a reference to
the traditional DNS port
- Route 53 - records
  - How do you want to route traffic for a domain
  - Each record contains:
  - Domain/subdomain Name – e.g., example.com
  - Record Type – e.g., A or AAAA
  - Value – e.g., 123.456.789.123
  - Routing Policy – how Route 53 responds to queries
  - TTL – amount of time the record cached at DNS Resolvers
  - Route 53 supports the following DNS record types:
  - (must know) A / AAAA / CNAME / NS
  - (advanced) CAA / DS / MX / NAPTR / PTR / SOA / TXT / SPF / SRV
- Route 53 record types
  - A – maps a hostname to IPv4
  - AAAA – maps a hostname to IPv6
  - CNAME – maps a hostname to another hostname
    - The target is a domain name that must have an A or AAAA record
    - Can’t create a CNAME record for the top node of a DNS namespace (Zone Apex)
    - Example: you can’t create for example.com, but you can create for www.example.com
  - NS – Name Servers for the Hosted Zone
  - Control how traffic is routed for a domain
- Route 53 - Hosted Zones
  - A container for records that define how to route traffic to a domain and its subdomains
  - Public Hosted Zones – contain records that specify how to route traffic on the Internet (public domain names) application1.mypublicdomain.com
  - Private Hosted Zones – contain records that specify how you route traffic within one or more VPCs (private domain names) application1.company.internal
  - You pay $0.50 per month per hosted zone
## 103 Route 53 - registering a domain
- Goto route53 - registered domains - select unique domain name 
- this is paid service of $13 per year here we have options to auto-renew and to privacy, mode to hide contact information 

## 104 Creating our first record
- route 53- hosted zones - click on the domain - create a record 
- give name as a prefix to your domain ex: api.example.com
- select type of records like A, AAAA 
- Enter IP address of EC2 or any other server for a new domain record
- Everything default and click create button 

- cloud shell - CLI
  - sudo yum install -y bind-utils
  - then search for the newly created domain record, it will retune inserted IP address
    - nslookup api.exaple.com
  - we can also use the dig command
    - dig api.example.com

## 105. Route  53 EC2 setup
- Create 3 EC2 instances in different regions and a load balancer for these ec2s
- Create EC2 instances internet facing and see if you are able to access them from the browser and also from load balancer URL


## 106. Route 53 TTL (Time To Live)
- time to live means we say the client to catch the result for a certain time
- If we kept more TTL then the client will lose latest that for that period
- If we keep low TTL then the client will keep connecting to route 53 to request data even if data have not changed
- Set TTL to 300secondas and use the `dig api.example.com` command to see the timer of cached seconds it should how much time the data will be under cached


## 107.Route 53 CNAME vs Alias
- AWS resource (load balancer, cloudfront) expose an AWS hostname:
  - lbl-123.us.east-2.slb.amazonaws.com and 
- CNAME:
  - Points a hostname to any other hostname. (app.mydomain.com => blabla.anything.com)
  - **ONLY FOR NON ROOT DOMAIN (aka. something.mydomain.com)**
- Alias:
  - Points a hostname to an AWS Resource (app.mydomain.com => blabla.amazonaws.com)
  - **Works for ROOT DOMAIN and NON ROOT DOMAIN (aka mydomain.com)**
  - Free of charge
  - Native health check

 - Route 53 - Alias records
  - Maps a hostname to an AWS resource
  - An extension to DNS functionality
  - Automatically recognizes changes in the resource’s IP addresses
  - Unlike CNAME, it can be used for the top node of a DNS namespace (Zone Apex), e.g.: example.com
  - Alias Record is always of type A/AAAA for AWS resources (IPv4 / IPv6)
  - You can’t set the TTL
- Route 53 - Alias records Targets
  - Elastic Load Balancers
  - CloudFront Distributions
  - API Gateway
  - Elastic Beanstalk environments
  - S3 Websites
  - VPC Interface Endpoints
  - Global Accelerator accelerator
  - Route 53 record in the same hosted zone
  - **You cannot set an ALIAS record for an EC2 DNS name**
- Creating CNAME record
  - GOto hosted zone give record name (**myapp**.example.com) 
  - select record type CNAME 
  - Give domain name value
  - Create record
- Create an alias record:
   - Goto hosted zone give record name (**myalias**.example.com) 
   - select record type = A 
   - turn the alias ON using the toggle button 
   - Select route traffic type from the list - alias to the app load balancer
     - select region -> load app balancer 
   - we have the option to evaluate health as it's ALB
   - Create record
 - In case you just want to redirect your main domain ex example.com using alias/CNAME wll not work
   - Goto hosted zone keep record name blank (example.com) 
   - select record type = A 
   - turn the alias ON using the toggle button 
   - Select route traffic type from the list - alias to the app load balancer
     - select region -> load app balancer 
   - we have the option to evaluate health as it's ALB
   - Create record
   
## 108. Routing Policy - simple
- Define how Route 53 responds to DNS queries
- Don’t get confused by the word “Routing”
  - It’s not the same as Load balancer routing which routes the traffic 
  -  DNS does not route any traffic, it only responds to the DNS queries
 - Route 53 Supports the following Routing Policies
  - Simple
  - Weighted
  - Failover
  - Latency based
  - Geolocation
  - Multi-Value Answer
  - Geoproximity (using Route 53 Traffic Flow feature)
-Create a simple routing policy
  - Goto hosted zone give record name (**any**.example.com) 
  - select record type A 
  - Enter multiple EC2 IPs
  - Select routing policy **simple**
  - Create record
  
## 109. Routing Policy - Weighted
- Weighted 
  - Control the % of the requests that go to each specific resource
  - Assign each record a relative weight:
    - 𝑡𝑟𝑎𝑓𝑓𝑖𝑐 (%) = Weight of record/(sum of the weight of all records)
  - Weights don’t need to sum up to 100
  - DNS records must have the same name and type
  - Can be associated with Health Checks
  - Use cases: load balancing between regions, testing new application versions…
  - **Assign a weight of 0 to a record to stop sending traffic to a resource**
  - **If all records have weight of 0, then all records will be returned equally**
-Create a weighted routing policy
  - Goto hosted zone give record name (**any**.example.com) 
  - select record type A 
  - Enter single EC2 IP
  - Select routing policy **weighted**
  - given weight 3 (works as ratio 3:2)
  - Provide your custom record ID (any name)
  - Click on Add records and follow the same steps for next EC2 ip
  - we can create multiple records with different weights 
  - Create record
 
 ## 110. Routing policy - Latency 
 
 -Create a latency routing policy
  - Goto hosted zone give record name (**any**.example.com) 
  - select record type A 
  - Enter single EC2 IP
  - Select routing policy **latency**
  - Select the region of the EC2 instance
  - Provide your custom record ID (any name)
  - Click on Add records and follow the same steps for next EC2 ip
  - we can create multiple records for different EC2 
  - Create record
  
  
 ## 110. Routing Policy - Health checks 
- HTTP Health Checks are only for **public resources**
- Health Check => Automated DNS Failover:
  1. Health checks that monitor an endpoint (application, server, other AWS resource)
  2. Health checks that monitor other health checks (Calculated Health Checks)
  3. Health checks that monitor CloudWatch Alarms (full control !!) – e.g., throttles of DynamoDB, alarms on RDS, custom metrics,… (helpful for private resources)
- Health Checks are integrated with CW metrics
- Health Checks – Monitor an Endpoint
  - About 15 global health checkers will check the endpoint health 
    - Healthy/Unhealthy Threshold – 3 (default)
    - Interval – 30 sec (can set to 10 sec – higher cost)
    - Supported protocol: HTTP, HTTPS, and TCP
    - If > 18% of health checkers report the endpoint is healthy, Route 53 considers it **Healthy**. Otherwise, it’s **Unhealthy**
    - Ability to choose which locations you want Route 53 to use
  - Health Checks pass only when the endpoint responds with the 2xx and 3xx status codes
  - Health Checks can be set up to pass/fail based on the text in the first **5120 bytes** of the response
  - Configure your router/firewall to allow incoming requests from Route 53 Health Checkers
 - Route 53 – Calculated Health Checks
   - Combine the results of multiple Health Checks into a single Health Check
  - You can use **OR, AND, or NOT**
  - Can monitor up to 256 Child Health Checks
  - Specify how many of the health checks need to pass to make the parent pass
  - Usage: perform maintenance to your website without causing all health checks to fail
 - Health Checks – Private Hosted Zones
  - Route 53 health checkers are outside the VPC
  - They can’t access private endpoints (private VPC or on-premises resources)
  - You can create a CloudWatch Metric and associate a CloudWatch Alarm, then create a Health Check that checks the alarm itself
