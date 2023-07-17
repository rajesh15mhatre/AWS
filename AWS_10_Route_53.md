# Section 10: Route 53 Not Feer Tire so hands on not possible 
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
  - Click button Create record


## 111. Route 53 - Health Checks
- Overview
  - HTTP Health Checks are only for public resources
  - Health Check => Automated DNS Failover:
    1. Health checks that monitor an endpoint (application, server, other AWS resource)
    2. Health checks that monitor other health checks (Calculated Health Checks)
    3. Health checks that monitor CloudWatch Alarms (full control !!) – e.g., throttles of DynamoDB, alarms on RDS, custom metrics, … (helpful for private resources)
  - Health Checks are integrated with CW
metrics
- Health CHceks - Monitor an EndPoint
  - **About 15 global health checkers will check the endpoint health**
    - Healthy/Unhealthy Threshold – 3 (default)
    - Interval – 30 sec (can set to 10 sec – higher cost)
    - Supported protocol: HTTP, HTTPS and TCP
    - If > 18% of health checkers report the endpoint is **healthy**, Route 53 considers it Healthy. Otherwise, it’s **Unhealthy**
    - Ability to choose which locations you want Route 53 to
use
  - Health Checks pass only when the endpoint responds with the 2xx and 3xx status codes
  - Health Checks can be setup to pass / fail based on the text in the first 5120 bytes of the response
  - Configure you router/firewall to allow incoming requests from Route 53 Health Checkers
- Route 53 – Calculated Health Checks
  - - Combine the results of multiple Health Checks into a single Health Check
  - You can use **OR, AND, or NOT**
  - Can monitor up to 256 Child Health Checks
  - Specify how many of the health checks need to pass to make the parent pass
  - Usage: perform maintenance to your website without causing all health checks to fail
 - Health Checks – Private Hosted Zones
  - Route 53 health checkers are outside the VPC
  - They can’t access private endpoints (private VPC or on-premises resources)
  - You can create a CloudWatch Metric and associate a CloudWatch Alarm, then create a Health Check that checks the alarm itself

## 112 Route 53 - Health Checks Hands on
- Create 3 health checks for all three EC2 instances
  - Route 53- health checks - create health checkes
  - givename region - **endpoint** - provide ip address
  - port 80 - prtocol http
  - provide path is application
  - advance config - standard check every 30 seconds
- delete http inbound rule so 1 ec2 instace will be shown unhealthy it take 10-15 minutes under health chek
- Calculated health cheks
   Route 53- health checks - create health checkes
  - givename region - **status of ither health checks** - select 3 health checks(earlier created) to be monitored
  - Select calculation under **report when **
    -  report when 1 of 3 are unhealthy 
    - report when all are unhealthy
    - report when either unhealthy
  - next create
## 113 Routing Policy - Failover
- in case of EC2 instace failover it will router to healthy EC2
- Create failover
  - route 53 - hosted zones - accoutn - Create record
  - give name - provice ip address and select routing policy to failover
  - select the failover record type as primary
  - select health check policy for that ec2
  - provide records ID
  - Add another records with other EC2 (failover Ec2) as
    - give name - provice ip address and select routing policy to failover
  - select the failover record type as secondary
  - select health check policy for that ec2
  - provide records ID
  - Create records
- test by removing http outbound rule from security group to see if secondary EC2 routee as primary is unhealthy
## 114 - Routing Policy - Geolocation
- Routing Policies
  - Different from Latency-based!
  - This routing is based on user location
  - Specify location by Continent, Country or by US State (if there’s overlapping, most precise location selected)
- Should create a “Default” record (in case there’s no match on location)
- Use cases: website localization, restrict content distribution, load balancing, …
- Can be associated with Health Checks
- Hands on
  - route 53 - hosted zones - accoutn - Create record
  - give name - provice ip address and select routing policy to failover
  - Select routing policy **Geolocation**
  - Select Country -US
  - Add another record - Repeat steps for othe 2 Ec2 instances for Asia and Europe
  -  Create Record
-  Try aceessing URL by changing your location via VPAN so when you are in India wyou will visit EC2 in Asia vice cersa
# 115 Routng policy - Geoproximity 
- Overview
  - Route traffic to your resources based on the geographic location of users and resources
  - Ability to shift more traffic to resources based on the defined bias
  - To change the size of the geographic region, specify bias values:
    - To expand (1 to 99) – more traffic to the resource
    - To shrink (-1 to -99) – less traffic to the resource
  - Resources can be:
    - AWS resources (specify AWS region)
    - Non-AWS resources (specify Latitude and Longitude)
  - You must use Route 53 Traffic Flow to use this feature
- Routing policy based on geolaction to shift traffic to spefic region based on biased
## 116. Routing Policy - IP -based 
- Overview
  - Routing based on client's IP address
  - You provide a list of CIDRs for ypur clients and the corresponding endpoints/location (use-IP-to-endpoints mapping)
  - Use cases: Optimaize performance, reduce noetwork costs
  - Examople : route end users from a particular ISP (range of IP address) to a specific endpoints(EC2)

## 117. Routing Pllicy - Multivalue 
- Overview  
  - Use when routing traffic to multiple resources
  - Route 53 return multiple values/resources
  - Can be associated with Health Cheks (return only value for healthy resources)
  - Up to 8 healthy records are returned for each multi-value query
  - Multi -value is not a substitute for having an ELB
  - It sis better than simple routing policy wiht mutiple ec2 ips as it randomkly retuns resource ans also health check is not poosible on simple multi-ip policy where as in Multi value we can have halth check for each value (EC2)
- Hands On
  - route 53 - hosted zones - accoutn - Create record
  - give name - provice ip address and select routing policy to failover
  - Select routing policy   - multi -value
  - Select TTL 1min
  - Select heacth check and provide record  id (any text)

## 118.3rd patry Domain & route 53
- Overview
  - You buy or register your domain name with a Domain Registrar typically by paying annual charges (e.g., GoDaddy, Amazon Registrar Inc., …)
  - The Domain Registrar usually provides you with a DNS service to manage your DNS records
  - But you can use another DNS service to manage your DNS records
  - Example: purchase the domain from GoDaddy and use Route 53 to manage your DNS records
- How to use Route 53 with 3rd party Domains
  - If you buy your domain on a 3rd party registrar, you can still use Route 53 as the DNS Service provider
  - Create a Hosted Zone in Route 53
  - Update NS Records on 3rd party website to use Route 53 Name Servers
  - Domain Registrar != DNS Service
  - But every Domain Registrar usually comes with some DNS features

## 119. Route 53 - section clean up
- Deleted all EC2 instanecs, Domaina host, records createed , taget groups ,  
