# Section 11: Amazon S3
Link : https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528314#overview
## 127. Overview
- Overview
  - Amazon S3 is one of the main building blocks of AWS
  - It’s advertised as ”infinitely scaling” storage
  - It’s widely popular and deserves its own section
  - Many websites use Amazon S3 as a backbone
  - Many AWS services uses Amazon S3 as an integration as well
  - We’ll have a step-by-step approach to S3
- Amazon Bucket
  - Amazon S3 allows people to store objects (files) in “buckets” (directories)
  - Buckets must have a globally unique name
  - Buckets are defined at the region level
  - Naming convention
    - No uppercase
    - No underscore
    - 3-63 characters long
    - Not an IP
    - Must start with lowercase letter or number
- Amazon S3 - objects
  - Objects (files) have a Key
  - The key is the FULL path:
    - s3://my-bucket/my_file.txt
    - s3://my-bucket/my_folder1/another_folder/my_file.txt
  - The key is composed of prefix + object name
    - s3://my-bucket/my_folder1/another_folder/my_file.txt
  - There’s no concept of “directories” within buckets (although the UI will trick you to think otherwise)
  - Just keys with very long names that contain slashes (“/”)
  - Object values are the content of the body:
    - Max Object Size is 5TB (5000GB)
    - If uploading more than 5GB, must use “multi-part upload”
  - Metadata (list of text key / value pairs – system or user metadata)
  - Tags (Unicode key / value pair – up to 10) – useful for security / lifecycle
  - Version ID (if versioning is enabled)

##128 S3 Hands on
- S3 - Bucket - create bucket - GIve name
- kepp default selection
  - Object ownership
  - Block public access
  - Versioning disabled
  - Encryption
- Create Bucket
- Upload file
- File will not be accessible usnig public url
- But will be accessible by clicking OPen button which uses pre-sign in url

## 129: S3 - Security
- Security
  - USer Based
    - IAM policies - which API calls should be allowed for a specific user from IAM console
  - Resource Based
    - Bucket Policies - bucket wide rules from the S3 console - allows cross account
    - Object Access Control List (ACL) – finer grain
    - Bucket Access Control List (ACL) – less common
  - Note: an IAM principal can access an S3 object if
    - the user IAM permissions allow it OR the resource policy ALLOWS it
    - AND there’s no explicit DENY
- S3 Bucket Policies
  - JSON based policies
    - Resources: buckets and objects
    - Actions: Set of APIs to Allow or Deny
    - Effect: Allow / Deny
   - Principal: The account or user to apply the policy to
  - Use S3 bucket for policy to:
    - Grant public access to the bucket
    - Force objects to be encrypted at upload
    - Grant access to another account (Cross
Account)
- Bucket settings for Block Public Access
  - Block public access to buckets and objects granted through
    - new access control lists (ACLs)
    - any access control lists (ACLs)
    - new public bucket or access point policies
  - Block public and cross-account access to buckets and objects through any public bucket or access point policies
  - **These settings were created to prevent company data leaks**
  - If you know your bucket should never be public, leave these on
  - Can be set at the account level


## 130: Securiry hands on 
- create policy to access bucket on internet 
  - S3 -bucket - permission  - allow public access
  - Bucket policy - policy generator
  - Select type s3 bucker
  - Efect allow
  - Pricipal *
  - serview - s3
  - ARN = bucket_name/*
  - Action = get objects
  - Save
- Test public URL

## 131: Static Website Hosting
- S3 can host static websites and have them accessible on the internet
• The website URL will be:(depending on region)
  - http://**bucket-name**.s3-website-**aws-region**.amazonaws.com
OR
  - - http://**bucket-name**.s3-website.**aws-region**.amazonaws.com
• If you get a 403 (Forbidden) error, make sure the bucket policy allows public reads!

## 132: S3 Websites Hands On
- Goto bucket - property - Enable Static website hosting (at the end of the page)
-  Index document =  index.html 
-  Save changes
-  Upload index.html
-  Copt URL from property and test it 

## 133: S3 versioning
- You can version your files in Amazon S3
- It is enabled at the **bucket level**
• Same key overwrite will increment the “version”: 1, 2, 3….
-  It is best practice to version your buckets
   - Protect against unintended deletes (ability to restore a version)
  - Easy roll back to previous version
- Notes:
  - Any file that is not versioned prior to enabling versioning will have version “null”
  - Suspending versioning does not delete the previous versions


## 134: S3 versioning Hands On
- Goto bucket - property - Enable bucket versioning
-  we can use toggle button show versions to see different file versions
-  When you delete file it just marked as deleted and can be recovered by deleting delete marker


## 135: S3 replication
- S3 Replication (CRR & SRR)
- **Must enable versioning** in source and destination
- Cross Region Replication (CRR)
- Same Region Replication (SRR)
- Buckets can be in different accounts
- Copying is asynchronous
- Must give proper IAM permissions to S3
- Use Cases:
  - CRR: compliance, lower latency access,
replication across accounts
  - SRR : log aggregation, live replication
between production and test accounts


## 136: Replication Note
- After activating, only new objects are replicated (not retroactive)
- Optionally, you can repliacte existing objects using **S3 Batch Repliation**
-  For DELETE operations:
- Can replicate delete markers from source to target (optional setting)
• Deletions with a version ID are not replicated (to avoid malicious deletes)
• There is no “chaining” of replication
• If bucket 1 has replication into bucket 2, which has replication into bucket 3
• Then objects created in bucket 1 are not replicated to bucket 3

## 137: Amazon S3 replication Hands On
- Create new bucket - in different region
- Enabled versioning - create bucket
- goto taget bucket - managememnt -replicaiton rule
-  give name - all obejct in bucket
- choose bucket in this account
- specify taget bucket
- crate a IAM rule
- try cresaig file and see it will replicated in taret buckert
- delete marker are by default not replicated you need to explicitly set then enable for replication . permenently delte are not replicated in any scenario


## 138 : s3 storage classes
- s3 storage classes
  - Amazon S3 Standard - General Purpose
  - Amazon S3 Standard-Infrequent Access (IA)
  - Amazon S3 One Zone-Infrequent Access
  - Amazon S3 Intelligent Tiering
  - Amazon Glacier
  - Amazon Glacier Deep Archive
  - Amazon S3 Reduced Redundancy Storage (deprecated - omitted)
- S3 Standard – General Purpose
  - High durability (99.999999999%) of objects across multiple AZ
  - If you store 10,000,000 objects with Amazon S3, you can on average expect to incur a loss of a single object once every 10,000 years
  - 99.99% Availability over a given year
  - Sustain 2 concurrent facility failures
  - Use Cases: Big Data analytics, mobile & gaming applications, content distribution…
- S3 Standard – Infrequent Access (IA)
  - Suitable for data that is less frequently accessed, but requires rapid access when needed
  - High durability (99.999999999%) of objects across multiple AZs
  - 99.9% Availability
  - Low cost compared to Amazon S3 Standard
  - Sustain 2 concurrent facility failures
  - Use Cases: As a data store for disaster recovery, backups…
- S3 One Zone - Infrequent Access (IA)
  - Same as IA but data is stored in a single AZ
  - High durability (99.999999999%) of objects in a single AZ; data lost when AZ is destroyed
  - 99.5% Availability
  - Low latency and high throughput performance
  - Supports SSL for data at transit and encryption at rest
  - Low cost compared to IA (by 20%)
  - Use Cases: Storing secondary backup copies of on-premise data, or storing data you can recreate
- S3 Intelligent Tiering
  - Same low latency and high throughput performance of S3 Standard
  - Small monthly monitoring and auto-tiering fee
  - Automatically moves objects between two access tiers based on changing access patterns
  - Designed for durability of 99.999999999% of objects across multiple Availability Zones
  - Resilient against events that impact an entire Availability Zone
  - Designed for 99.9% availability over a given year
- Amazon Glacier
 - Low cost object storage meant for archiving / backup
  - Data is retained for the longer term (10s of years)
  - Alternative to on-premise magnetic tape storage
  - Average annual durability is 99.999999999%
  - Cost per storage per month ($0.004 / GB) + retrieval cost
  - Each item in Glacier is called “Archive” (up to 40TB)
  - Archives are stored in ”Vaults”
- Amazon Glacier & Glacier Deep Archive
  - Amazon Glacier – 3 retrieval options:
    - Expedited (1 to 5 minutes)
    - Standard (3 to 5 hours)
    - Bulk (5 to 12 hours)
    - Minimum storage duration of 90 days
- Amazon Glacier Deep Archive – for long term storage – cheaper:
    - Standard (12 hours)
    - Bulk (48 hours)
    - Minimum storage duration of 180 days

## 139: S3 storage classe Hands On
- Create new bucket - class_demo
- Upload file
  - Under storage class select suitable class
- Under management - lifecycle rule - give name
- Apply to all objects
- Select rukle tomove files to differnant classes

## Quiz
1. You have a 25 GB file that you're trying to upload to S3 but you're getting errors. What is a possible solution for this?
- File size limit on s3 is 5GB
- update your bucket policy to allow larger files
- **Use multipart upload when uploading files larger than 5GB** (Multi-Part Upload is recommended as soon as the file is over 100 MB.)
- Encrypt the file

2. You're getting errors while trying to create a new S3 bucket named "dev". You're using a new AWS Account with no S3 buckets created before. What is a possible cause for this?
  - You are missing IAM permission to create an s3 bucket
  - **S3 bucket names must be globally unique and is already taken**

3. You have enabled versioning in your S3 bucket which already contains a lot of files. Which version will the existing files have?
  - **null**

4.  You have updated an S3 bucket policy to allow IAM users to read/write files in the S3 bucket, but one of the users complain that he can't perform a **PutObject** API call. What is a possible cause for this?
  - THe s3 bucket policy must be wrong
  - The user is lacking permissions
  - The IAM user must have an explicit DENY in the attched IAM policy (Explicit DENY in an IAM Policy will take precedence over an S3 bucket policy.)
  - You need to contact AWS support to lift this limit

5. You want the content of an S3 bucket to be fully available in different AWS Regions. That will help your team perform data analysis at the lowest latency and cost possible. What S3 feature should you use?
  - Amazon CloudFront Distribution
  - S3 Versioning
  - S3 Static Website Hosting
  - **S3 Replication** (S3 Replication allows you to replicate data from an S3 bucket to another in the same/different AWS Region.)

6. You have 3 S3 buckets. One source bucket A, and two destination buckets B and C in different AWS Regions. You want to replicate objects from bucket A to both bucket B and C. How would you achieve this?
  - **Configure replication from bucket A to B, then from bucket A to C**
  - Configure replication  from bucket A to B and B to C
  - Configure replication from bucket A to C and C to B

7. Which of the following is NOT a Glacier Deep Archive retrival mode?
  - **Expedited (1-5 min)**
  - Std (12 Hours)
  - Bulk (48 Hours)

8. Which of the following is NOT a Glacier Flexible retrieval mode?
  - **Instant (10 sec)**
  - Expedited (1-5 min)
  - Std (3-5 Hours)
  - Bulk (5-12 Hours)

