# Section 19: Serverless Overviews from a Solution Architect Perspective
Link: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/19736538#overview

## 212. Just a note that it is a light intro to serverless

## 213. Serverless Introduction
- What’s serverless
  - Serverless is a new paradigm in which the developers don’t have to manage servers anymore…
  - They just deploy code
  - They just deploy… functions!
  - Initially... Serverless == FaaS (Function as a Service)
  - Serverless was pioneered by AWS Lambda but now also includes anything that’s managed: “databases, messaging, storage, etc.”
  - Serverless does not mean there are no servers… it means you just don’t manage / provision / see them
- Serverless in AWS
  - AWS Lambda
  - DynamoDB
  - AWS Cognito
  - AWS API Gateway
  - Amazon S3
  - AWS SNS & SQS
  - AWS Kinesis Data Firehose
  - Aurora Serverless
  - Step Functions
  - Fargate

## 214. Lambda Overview
- Why AWS Lambda
- Because EC2:
  - Virtual Servers in the Cloud
  - Limited by RAM and CPU
  - Continuously running
  - Scaling means intervention to add/remove servers
- And Lambda is:
  - Virtual **functions** – no servers to manage!
  - Limited by time - **short executions**
  - Run **on-demand**
  - Scaling is **automated**!
- Benefits of AWS Lambda
  - Easy Pricing:
    - Pay per request and compute time
    - Free tier of 1,000,000 AWS Lambda requests and 400,000 GBs of compute time
  - Integrated with the whole AWS suite of services
  - Integrated with many programming languages
  - Easy monitoring through AWS CloudWatch
  - Easy to get more resources per function (up to 10GB of RAM!)
  - Increasing RAM will also improve CPU and network
- AWS Lambda language support
  - Node.js (JavaScript)
  - Python
  - Java (Java 8 compatible)
  - C# (.NET Core)
  - Golang
  - C# / Powershell
  - Ruby
  - Custom Runtime API (community supported, example Rust)
  - Lambda Container Image
    - The container image must implement the Lambda Runtime API
    - ECS / Fargate is preferred for running arbitrary Docker images
- Lambda integration example
 - We can create a Lambda function to create a thumbnail when the image is uploaded and store it in another S3 bucket
 - We can create serverless cron job-like tasks by using CludWatch to trigger the Lambda function as per the required frequency like every hour
- AWS Lambda Pricing: example
  - You can find overall pricing information here:
https://aws.amazon.com/lambda/pricing/
  - Pay per **calls**:
    The first 1,000,000 requests are free
    - $0.20 per 1 million requests thereafter ($0.0000002 per request)
  - Pay per **duration**: (in increments of 1 ms)
    - 400,000 GB-seconds of computing time per month for FREE
    - == 400,000 seconds if the function is 1GB RAM
    - == 3,200,000 seconds if the function is 128 MB RAM
    - After that $1.00 for 600,000 GB-seconds
  - It is usually very cheap to run AWS Lambda so it’s very popular 

## 215. Lambda Hands-On
- Created a test python job in lambda

## 216. Lambda Limits
- Execution:
  - Memory allocation: 128 MB – 10GB (1 MB increments)
  - Maximum execution time: 900 seconds (15 minutes)
  - Environment variables (4 KB)
  - Disk capacity in the “function container” (in /tmp): 512 MB to 10GB
  - Concurrency executions: 1000 (can be increased)
- Deployment:
  - Lambda function deployment size (compressed .zip): 50 MB
  - Size of uncompressed deployment (code + dependencies): 250 MB
  - Can use the /tmp directory to load other files at startup
  - Size of environment variables: 4 KB

## 217. Lambda@Edge & Clodfront Fucntions
- Customization At The Edge
  - Many modern applications execute some form of the logic at the edge
  - **Edge Function**:
    - A code that you write and attach to CloudFront distributions
    - Runs close to your users to minimize latency
  - CloudFront provides two types: **CloudFront Functions** & **Lambda@Edge**
  - You don’t have to manage any servers, deployed globally
  - Use case: customize the CDN content
  - Pay only for what you use
  - Fully serverless
- CloudFront Functions & Lambda@Edge
Use Cases
  - Website Security and Privacy
  - Dynamic Web Application at the Edge
  - Search Engine Optimization (SEO)
  - Intelligently Route Across Origins and Data Centers
  - Bot Mitigation at the Edge
  - Real-time Image Transformation
  - A/B Testing
  - User Authentication and Authorization
  - User Prioritization
  - User Tracking and Analytics
- CloudFront Functions
  - Lightweight functions written in JavaScript
  - For high-scale, latency-sensitive CDN customizations
  - Sub-ms startup times, **millions of requests/second**
  - Used to change Viewer requests and responses:
    - **Viewer Request**: after CloudFront receives a request from a
viewer
    - **Viewer Response**: before CloudFront forwards the response
to the viewer
  - Native feature of CloudFront (manage code entirely within CloudFront)
- Lambda@Edge
  - Lambda functions written in NodeJS or Python
  - Scales to **1000s of requests/second**
  - Used to change CloudFront requests and responses:
    - **Viewer Request** – after CloudFront receives a request from a viewer
    - **Origin Request** – before CloudFront forwards the request to the origin
    - **Origin Response** – after CloudFront receives the response from the origin
    - **Viewer Response** – before CloudFront forwards the response to the viewer
  - Author your functions in one AWS Region (us-east-1), then
CloudFront replicates its locations

- CloudFront Functions vs. Lambda@Edge - Use Cases
  - CloudFront Functions
    - Cache key normalization
      - Transform request attributes (headers, cookies, query strings, URL) to create an optimal Cache Key
    - Header manipulation
    - Insert/modify/delete HTTP headers in the
request or response
    - URL rewrites or redirects
    - Request authentication & authorization
    - Create and validate user-generated
tokens (e.g., JWT) to allow/deny requests
- Lamdba@Edge
  - Longer execution time (several ms
  - Adjustable CPU or memory
  - Your code depends on a 3rd library (e.g., AWS SDK to access other AWS services)
- Network access to use external services for processing
- File system access or access to the body of HTTP requests

# Read PDF notes for the next topics

## 218. Lambda in VPC
## 219. RDS - Invoking Lambda & Event Notifications
## 220. Amazon DynamoDB
## 221. Hands-on
## 222. Amazon DynamoDB - Adance Features
## 223. API Gateway Overview
## 224. API Gateway Hands on
## 225. Step Functions
## 226. Amazon Cognito overview
Quiz

