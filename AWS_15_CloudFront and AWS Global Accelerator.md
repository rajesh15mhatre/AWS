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
## 170. AWS Global Accelerator - Hands-on 
- It's not free
- Create 2 instances by selecting different  regions one by one and hardcode region names in the web app user script 
- Search for AWS Global Accelerator
- Create accelerator -given name - port:80, protocol: TCP  - next 
- select region - trafic 100 -
- Health check port and protocol - 80 and HTTP
- path - /
- interval 10
- threshold count - 3 -Next 
- Add another endpoint for EC2 instance for both the  regions
- weight - 120
- Click Create button 
- test the accelerator URL by using VPN from the region you selected and you can see the request would be router to the nearest EC2

## Quiz - CloudFront 
1. You have a CloudFront Distribution that serves your website hosted on a fleet of EC2 instances behind an Application Load Balancer. All your clients are from the United States, but you found that some malicious requests are coming from other countries. What should you do to only allow users from the US and block other countries?
- **Use CloudFront Geo Restriction**
- Use Origin Access Control
- Set up a security group and attach it to your CloudFront Distribution
- Setup a security group and attche it to your CloudFront Distribution
- Use a Route 53 Latency record and attched it to CloudFront

2. You have a static website hosted on an S3 bucket. You have created a CloudFront Distribution that points to your S3 bucket to better serve your requests and improve performance. After a while, you noticed that users can still access your website directly from the S3 bucket. You want to enforce users to access the website only through CloudFront. How would you achieve that?
- Send email to your client and tell them to not use the s3 endpoint
- **Configure your CloudFront Distribution and create an Origin Access Control OAC, then update your S3 bucket policy to only accept request from your CloudFront Distribution**
- Use s3 Access Point to redirect client to CloudFront

3. What does this S3 bucket policy do?

{

    "Version": "2012-10-17",

    "Id": "Mystery policy",

    "Statement": [{

        "Sid": "What could it be?",

        "Effect": "Allow",

        "Principal": {

           "Service": "cloudfront.amazonaws.com"

        },

        "Action": "s3:GetObject",

        "Resource": "arn:aws:s3:::examplebucket/*",
        "Condition": {
            "StringEquals": {
                "AWS:SourceArn": "arn:aws:cloudfront::123456789012:distribution/EDFDVBD6EXAMPLE"
            }
        }

    }]

}

- Forces GetObject request to be encrypted if coming fron cloudfront
- **Only allows the s3 bucket to be accessed from your cloudfront distribution**
- only allows getobject type of request on the s3 bucket fron anybody

4. A WordPress website is hosted in a set of EC2 instances in an EC2 Auto Scaling Group and fronted by a CloudFront Distribution which is configured to cache the content for 3 days. You have released a new version of the website and want to release it immediately to production without waiting for 3 days for the cached content to be expired. What is the easiest and most efficient way to solve this?
- Open a support ticket withAWS to remove the cloudfront cache
- **CloudFront Cache invalidaiton**
- EC2 cahce invalidation

5. A company is deploying a media-sharing website to AWS. They are going to use CloudFront to deliver the context with low latency to their customers where they are located in both US and Europe only. After a while there a huge costs for CloudFront. Which CloudFront feature allows you to decrease costs by targeting only US and Europe?
- CloudFront Cache Invalidation
- C**loudFront Price classes**
- CloudFront Cache Behaviour
- Origin Access Control

6. A company is migrating a web application to AWS Cloud and they are going to use a set of EC2 instances in an EC2 Auto Scaling Group. The web application is made of multiple components so they will need a host-based routing feature to route to specific web application components. This web application is used by many customers and therefore the web application must have a static IP address so it can be whitelisted by the customers’ firewalls. As the customers are distributed around the world, the web application must also provide low latency to all customers. Which AWS service can help you to assign a static IP address and provide low latency across the globe?
- **AWS Global Accelerator+ ALB**
- Amazon CloudFront
- NLB
- ALB


