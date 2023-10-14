# Section 25: IAM Advanced
## 283. Organisations Overview
- SCP (Service Control Policy) to control AWS access to child accounts. we can create OU( Org unit) to separate child aws account for ease of access and billing
## 284. Organisation Hands-on
## 285. IAM Advanced Policies
## 286. IAM - Resource vs role base policy
  - Resource-based policy services: Lambda, SNS, SQS, CloudWatch Logs, API Gateway..
  - Role-based policy services: Kinesis Streams, System Manager run commands, ECS Tasks....
## 287. IAM - Policy Evaluation Logic
- Event use has admin access and under boundary section if a user is only allowed for S3 access then he will only get access to S3
- User will only get access to common services access specified in:
  - Organisation SCP
  - Resource-based policy
  - Permission Boundary
  - Identity-Based Policy
  - Session policy (STS)
## 288. AWS IAM Identity Center ( SSO earlier)
- Centralized access portal for SSO and other
- We can create Permission sets out of OU Roles and assign them to users.
- We can use Attribute Based Access Control (ABAC) based on the user's attributes stored in the IAM Identity Center
## 289. AWS Active Directory
## 290. AWS Directory - Hands On
## 291. AWS Control Tower Service
## Quiz
1. You have strong regulatory requirements to only allow fully internally audited AWS services in production. You still want to allow your teams to experiment in a development environment while services are being audited. How can you best set this up?
- Provide the Dev team with a completely independent AWS account
- Apply a global IAM policy on your Prod account
- **Create an AWS Organization and create two prod and Dev OUs, then Apply an SCP on the Prod OU**
- Create an AWS Config Rule
  
2. You are managing the AWS account for your company, and you want to give one of the developers access to read files from an S3 bucket. You have updated the bucket policy to this, but he still can't access the files in the bucket. What is the problem?

{

    "Version": "2012-10-17",

    "Statement": [{

        "Sid": "AllowsRead",

        "Effect": "Allow",

        "Principal": {

            "AWS": "arn:aws:iam::123456789012:user/Dave"

        },

        "Action": "s3:GetObject",

        "Resource": "arn:aws:s3:::static-files-bucket-xxx"

     }]

}
- Everything is okay, he just needs to logout and log in again
- The bucker does not contain any files yet
- **You should change the resource to `arn:aws:s3:::static-files-bucket-xxx/*`, because this is an object-level permission**

3. You have 5 AWS Accounts that you manage using AWS Organizations. You want to restrict access to certain AWS services in each account. How should you do that?
- Using IAM Roles
- **Using AWS Orgnisations SCP**
- Using AWS Config
  
4. Which of the following IAM condition key you can use only to allow API calls to a specified AWS region?
- aws:RequiredRegion
- aws:SourceRegion
- aws:InitilRegion
- **aws:RequestedRegion**
  
5. When configuring permissions for EventBridge to configure a Lambda function as a target you should use ………………….. but when you want to configure a Kinesis Data Streams as a target you should use …………………..
- Identity-based policy, Resource-Based Policy
-** Resource-based policy, Identity-Based Policy**
- Identity-based policy, Identity-based policy
- Resource-based policy, Resource-based policy


















