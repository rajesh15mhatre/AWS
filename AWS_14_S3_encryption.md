# Section 14 : S3 Encryption
Link: udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528318#overview

## 148: S3 Encryption
- There are 4 methods of encrypting objects in S3
  - Server side Encryption (SSE):
    - **Server -side Encryption with Amazon S3 managed keys(SSE-s3) - Enabled by default**
      - SSE-S3: encrypts S3 objects using keys handled & managed by AWS
    - **Server -side Encryption with KMS key stored in AWS KMS**
      - SSE-KMS: leverage AWS Key Management Service to manage encryption keys
   - **Server -side Encryption with Customer -provided Keys**
     - SSE-C: when you want to manage your own encryption keys
  - **Client Side Encryption**
- It’s important to understand which ones are adapted to which situation
for the exam
- SSE-S3
  - SSE-S3: encryption using keys handled & managed by Amazon S3
  - Object is encrypted server side\
  - AES-256 encryption type
  - Must set header: “x-amz-server-side-encryption": "AES256"
- SSE- KMS
  - SSE-KMS: encryption using keys handled & managed by KMS
  - KMS Advantages: user control + audit trail
  - Object is encrypted server side
  - Must set header: “x-amz-server-side-encryption": ”aws:kms"
- SSE- C
  - SSE-C: server-side encryption using data keys fully managed by the customer outside of AWS
  - Amazon S3 does not store the encryption key you provide
  - HTTPS must be used
  - Encryption key must provided in HTTP headers, for every HTTP request made
- Client side Encryption
  - Client library such as the Amazon S3 Encryption Client
  - Clients must encrypt data themselves before sending to S3
  - Clients must decrypt data themselves when retrieving from S3
  - Customer fully manages the keys and encryption cycle 
- Encryption in transit (SSL/TLS)
  - Amazon S3 exposes 2 endpoints:
    - HTTP endpoint: non encrypted
    - HTTPS endpoint: encryption in flight
  - You’re free to use the endpoint you want, but HTTPS is recommended
  - Most clients would use the HTTPS endpoint by default
  - HTTPS is mandatory for SSE-C
  - Encryption in flight is also called SSL / TLS
- Force Encryption
  - WE can create a IAM policy to only allow https request by adding condition aws:SecureTransport: false on GetObject action 

## 149: S3 Encryption - Hands On
- While creating buket we get option to specify encryption
- Create bucket with Default encryption SSE-S3
- Upload a test file and check server side encryption settings you will see SSE-s3
- We can also edit this
  - to change encryption to KMS
  - Select default KMS key
  - Save
- Now we can see teo version of files with different encryptions as versioning is enabled
- SSE-C option isnot suported on Portal its only available in CLI SDK
-  Client side Encryption is nothing to do it AWS

## 150: S3 Default Encryption 
- **SSE -S3 encryption is automatically applied to new objects stored in S3 bucket**
- Optionally, you can force encryption using a bucket policy and refuse any API call to PUT an S3 Obect without encryption headers (SSE-KMS or SSE-C) 
- **Note: Bucket Policies are evaluated before “default encryption”
**

## 151: S3 CORS
- Cross-Origin Resiource sharing (CORS)
- Website
  - Origin = scheme(protocol) + host (doman) + port
    - ex. https://www.example.com/ port 80 for http and 43 for https
  - **Web Browser** based mechanism to allow requests to other origins while visiting the main origin
  - Same origin: http://example.com/app1 & http://example.com/app2
  - Different origins: http://www.example.com & http://other.example.com
  - The requests won’t be fulfilled unless the other origin allows for the requests, using CORS Headers (ex: Access-Control-Allow-Origin)

- S3 - CORS
  - If a client does a cross-origin request on our S3 bucket, we need to enable the correct CORS headers
  - It’s a popular exam question
  - You can allow for a specific origin or for * (all origins)

## 152. S3 CORS Hands On
- Uncomment sacript part under  index.html orivided in material
- Goto bucket upload index.html and extra-file.html
- Create another bucket demo-other-rigin-rajeh
- Enable static webhosting website
- make bucket public under permission- bucket policy 
- Upload extra page html
- add public url of extra page from other region bucket and add it into index .html file and overwrite it in main bucket
- load index.html in main buket and check error in chrome deveopler tab -networking tab - chec header
- now add CORS setting i other region bucker
- Copy CORS json and paste it under permission - CORS sessings
- fill proper URL in json 

## 153. MFA Delete
- MFA (multi factor authentication) forces user to generate a code on a device (usually a mobile phone or hardware) before doing important operations on S3
- To use MFA-Delete, **Versioning must be enabled**  on the S3 bucket
- You will need MFA to
  - permanently delete an object version
  - suspend versioning on the bucket
- You won’t need MFA for
  - enabling versioning
  - listing deleted versions
- **Only the bucket owner (root account) can enable/disable MFA-Delete**
• MFA-Delete currently can only be enabled using the CLI


# 154. S3 MFA Delete Hands On
- Create bucket with versioning
- properties - bucket versioning - edit
- MFA is diabled  (we can enabled it using cli by root user)
  - pre requisite is to create MFA unser IAM
    - root acount - my security credentials - Under MFA is shown (ARN= Amazon resource name)
    - set up IAM profle using mfs-delete.sh file provided in resources
  - Now multifactor delete is enabled
  - Now we can only delete file permenantly using MFA CLI

## 155. S3 Access Logs
- Overview
  - For audit purpose, you may want to log all access to S3 buckets
  - Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket
  - That data can be analyzed using data analysis tools…
  - Or Amazon Athena as we’ll see later in this section!
  - The log format is at: https://docs.aws.amazon.com/AmazonS3/latest/dev/LogFormat.html
- Warning
  - Do not set your logging bucket to be the monitored bucket
  - It will create a logging loop, and your bucket will grow in size exponentially

# 156. S3 Access logs - Hands on
- Create a bucket to store logs
- bvuket on which logs needed - property  - Server access logging - Enable - select logging bucket
- Add / access/ delete file and test the logs

## 157 S3 pre-Signed URLs
- Generate pre-signed URLs using the S3 Console, SDK or AWS CLI
  - For downloads (easy, can use the CLI)
  - For uploads (harder, must use the SDK)
- URL Expiration
  - S3 Console - 1 min upto 720 min(12 hr)
  - AWS CLI - configure expiration with -expires-in parameter in seconds (default 3600 sec, max 604800 sec ~ 168 hr)
- Users given a pre-signed URL inherit the permissions of the person who generated the URL for GET / PUT
- Examples :
  - Allow only logged-in users to download a premium video on your S3 bucket
  - Allow an ever changing list of users to download files by generating URLs dynamically
  - Allow temporarily a user to upload a file to a precise location in our bucket

## 158. S3 Pre-signed URLs - Hands on
- goto private file - object action - share a presigned URL
- set time for validity  
- share and test

## 159. Glacier Vault Lock & S3 Object Lock
- Glacier Vault Lock
  - Adopt a WORM (Write Once Read Many) model
  - Lock the policy for future edits (can no longer be changed)
  - Helpful for compliance and data retention
- S3 Object Lock (versioning must be enabled)
  - Adopt a WORM (Write Once Read Many) model
  - Block an object version deletion for a specified amount of time
  - Object retention:
    - **Retention Period: protect a object for specified period, it can be extended**
    - Legal Hold:
      - Protect object, no expiry date for retention
      - Can be freely placed and removed using the s3:PutObjectLegalHold IAM permission
  - Modes:
    - **Governance mode**: users can't overwrite or delete an object version or alter its lock settings unless they have special permissions
    - **Compliance mode**: a protected object version can't be overwritten or deleted by any user, including the root user in your AWS account. When an object is locked in compliance mode, its retention mode can't be changed, and its retention period can't be shortened.

## 160. S3 Access Points
- Access point
  - Access points simplify security management for s3 bucket
  - Each Access Point has:
    - its own DNS name (internet or VPC origin)
    - an access point policy (similar to bucket policy) - manage security at scale
- VPC origin
  - We can define the access point to be accessible only from within the VPC
  - You must create a VPC Endpoint to access the Access Point (Gateway or Interface Endpoint)
  - The VPC Endpoint policy must allow access to the target bucket and Access point
  
## 161. S3 Access Object Lambda
- Use AWS Lambda functions to change the object before it is retrived by the caller application
- Only one s3 bucket is needed, on rop of which we create s3 access point and s3 object lambda access point
- Use case
  - Redacting personally identifiable information for analyticsd or non-orodcution env
  - Converting accross data foramts, such as converting XML to JSON
  - Resizing and watermarking images on the fly using caller -seocific datail such as the user who requiested the object

## Quiz
1. Your client wants to make sure that file encryption is happening in S3, but he wants to fully manage the encryption keys and never store them in AWS. You recommend him to use ............................
 - SSE-S3
 - SSE-KMS
 - **SSE-C** (With SSE-C, the encryption happens in AWS and you have full control over the encryption keys.)
 - Client-side Encryption
  
2. A company you're working for wants their data stored in S3 to be encrypted. They don't mind the encryption keys stored and managed by AWS, but they want to maintain control over the rotation policy of the encryption keys. You recommend them to use ....................
  - SSE-S3
  - **SSE-KMS** (With SSE-KMS, the encryption happens in AWS, and the encryption keys are managed by AWS but you have full control over the rotation policy of the encryption key. Encryption keys stored in AWS.)
  - SSE-C
  - Client-side Encryption

3. Your company does not trust AWS for the encryption process and wants it to happen on the application. You recommend them to use ....................
 - SSE-S3
 - SSE-KMS
 - SSE-C
 - **Client-side Encryption** (With Client-Side Encryption, you have to do the encryption yourself and you have full control over the encryption keys. You perform the encryption yourself and send the encrypted data to AWS. AWS does not know your encryption keys and cannot decrypt your data.)

4. You have a website that loads files from an S3 bucket. When you try the URL of the files directly in your Chrome browser it works, but when the website you're visiting tries to load these files it doesn't. What's the problem?
  - The Bucket policy is wrong
  - The IAM policy is wrong
  - **CORS is wrong** (Cross-Origin Resource Sharing (CORS) defines a way for client web applications that are loaded in one domain to interact with resources in a different domain. To learn more about CORS, go here: https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html)
  - Encryption is wrong 

5. An e-commerce company has its customers and orders data stored in an S3 bucket. The company’s CEO wants to generate a report to show the list of customers and the revenue for each customer. Customer data stored in files on the S3 bucket has sensitive information that we don’t want to expose in the report. How do you recommend the report can be created without exposing sensitive information?
  - **Use s3 object lambda to change the object before they are retrieved by the report generator app**
  - Create another s3 bucket. Create a lambda function to process each file, remove the sensitive information and then move them to the new s3 bucket
  - Use s3 object lock to lock the sensitive information from being fetched bt the report generator app 


6. You suspect that some of your employees try to access files in an S3 bucket that they don't have access to. How can you verify this is indeed the case without them noticing?
  - **Enable s3 access logs and analyze them using Athena**(S3 Access Logs log all the requests made to S3 buckets and Amazon Athena can then be used to run serverless analytics on top of the log files.)
  - Restrict their IAM policy and look at CloudTail Logs
  - User a bucket policy

7. You are looking to provide temporary URLs to a growing list of federated users to allow them to perform a file upload on your S3 bucket to a specific location. What should you use?
  - S3 CORS
  -** S3 pre-signed URL** (S3 Pre-Signed URLs are temporary URLs that you generate to grant time-limited access to some actions in your S3 bucket.)
  - S3 Bycket policies


8. For compliance reasons, your company has a policy mandate that database backups must be retained for 4 years. It shouldn't be possible to erase them. What do you recommend?
  - **Glacier Vaults with Vault lock policies**
  - EFS network drives with restrictive Linux permissions
  - S3 with bucket policies

9. You would like all your files in an S3 bucket to be encrypted by default. What is the optimal way of achieving this?
  - Use a bucket policy that forces HTTPS connections
  - **Do nothing, S3 automatically encreypt nre object usong SSE with s3 managed keys (SSE-S3)**
  - Enable versioning
  
10. You have enabled versioning and want to be extra careful when it comes to deleting files on an S3 bucket. What should you enable to prevent accidental permanent deletions?
  - Use a bucket policy
  - **Enable MFA delete** (MFA Delete forces users to use MFA codes before deleting S3 objects. It's an extra level of security to prevent accidental deletions.)
  - Encrypt the files
  - Disable versioning

11. A company has its data and files stored on some S3 buckets. Some of these files need to be kept for a predefined period of time and protected from being overwritten and deletion according to company compliance policy. Which S3 feature helps you in doing this?
  - S3 object Lock - retension governance mode
  - S3 versioning
  - **S3 object lock - retention compliance mode**
  - S3 Glacier Valut Lock

12. Which of the following S3 Object Lock configuration allows you to prevent an object or its versions from being overwritten or deleted indefinitely and gives you the ability to remove it manually?
  - Rentention Governance mode
  - Retention complaince mode
  - **Legal Hold**






