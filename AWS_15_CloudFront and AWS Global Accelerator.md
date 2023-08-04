# Section 15: ClloudFront and AWS Global Accelerator
Link: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/quiz/5684344#overview

## 162: CloudFront Overview
- Overview
  - Content Delivery Network (CDN)
  - **Improves read performance, content
is cached at the edge**
  - 216 Point of Presence globally (edge
locations)
  - **DDoS protection, integration with
Shield, AWS Web Application
Firewall**
  - Can expose external HTTPS and
can talk to internal HTTPS backends
- CloudFront – Origins
  - S3 bucket
    - For distributing files and caching them at the edge
    - Enhanced security with CloudFront **Origin Access Control (OAC)**
    - OAC is replacing Origin Access Identity (OAI)
    - CloudFront can be used as an ingress (to upload files to S3)
  - Custom Origin (HTTP)
    - Application Load Balancer
    - EC2 instance
    - S3 website (must first enable the
- CloudFront vs S3 Cross Region Replication
  - CloudFront:
    - Global Edge network
    - Files are cached for a TTL (maybe a day)
    - **Great for static content that must be available everywhere**
  - S3 Cross Region Replication:
    - Must be setup for each region you want replication to happen
    - Files are updated in near real-time
    - Read only
    - **Great for dynamic content that needs to be available at low-latency in few regions**

## 163. ClooudFront with s3 - Hans on
- Create a bucket with deafalut settings
- Upload files
- goto cloudFront -create distribution - choose origin
  - access- origin access control - cerate control setting - give name - create
  - Disable firewall
  - Default object = index.html
- goto ditribution - security -  edit origin - click copy policy button 
- Paste policy to s3 bucket policy
- test it by copying dsn from cloud front check it in browser

## 164. CloudFront  - ALB as an origin 
- Need to allow all edge location public IPs in SG(security group) of EC2 to get access on CloudFront
- Same with the ALB we need to allow all Edge location IP address in SG of ALB and  EC2s connection can be private only accessible to ALB
- we will get all public IP of Edge from AWS

## 165. CloudFront  - Geo Restriction
- Overview
  - You can restrict who can access your distribution
  - Whitelist: Allow your users to access your content only if they're in one of the countries on a list of approved countries.
  - Blacklist: Prevent your users from accessing your content if they're in one of the countries on a blacklist of banned countries.
  - The “country” is determined using a 3rd party Geo-IP database
  - Use case: Copyright Laws to control access to content
- Hands On
  - Distribution - Edit geographical restriction
  - paste allow/block list of IPs

## 166. CloudFront - Price Classes
- CloudFront Edge locations are all around the world
- The cost of data out per edge location varies
- Price classes
  - You can reduce the number of edge locations for cost reduction
  - Three price classes:
    1. Price Class All: all regions – best performance
    2. Price Class 200: most regions, but excludes the most expensive regions
    3. Price Class 100: only the least expensive regions

## 167. CloudFront -Cache- invalidations
- In case you update the back-end origin, CloudFront doesn’t know about it and will only get the refreshed content after the TTL has expired
- However, you can force an entire or partial cache refresh (thus bypassing the TTL) by performing a CloudFront Invalidation
- You can invalidate all files (*) or a special path (/images/*)

## 168. AWS Global Accelerator
- Scenario
  - You have deployed anapplication and have global users who want to access it directly.
  - They go over the publicinternet, which can add a lot of latency due to many hops
  - We wish to go as fast as possible through AWS network to minimize latency
- Unicast IP vs Anycast IP
  - Unicast IP: one server holds one IP address
  - Anycast IP: all servers hold the same IP address and the client is routed to the nearest one
- Solution
  - Leverage the AWS internal network to route to your application
  - 2 Anycast IP are created for your application
  - The Anycast IP send traffic directly to Edge Locations
  - The Edge locations send the traffic to your application
- Use cases
  - Works with Elastic IP, EC2 instances, ALB, NLB, public or private
  -  Consistent Performance
    - Intelligent routing to lowest latency and fast regional failover
    - No issue with client cache (because the IP doesn’t change)
    - Internal AWS network
  - Health Checks
    - Global Accelerator performs a health check of your applications
    - Helps make your application global (failover less than 1 minute for unhealthy)
    - Great for disaster recovery (thanks to the health checks)
  - Security
    - only 2 external IP need to be whitelisted
    - DDoS protection thanks to AWS Shield
- AWS Global Accelerator vs CloudFront
  - They both use the AWS global network and its edge locations around the world
  - Both services integrate with AWS Shield for DDoS protection.
  - CloudFront
    - Improves performance for both cacheable content (such as images and videos)
    - Dynamic content (such as API acceleration and dynamic site delivery)
    - Content is served at the edge
  - Global Accelerator
   - Improves performance for a wide range of applications over TCP or UDP
   - Proxying packets at the edge to applications running in one or more AWS Regions.
   - Good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP
   - Good for HTTP use cases that require static IP addresses
   - Good for HTTP use cases that required deterministic, fast regional failover




