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

# 152. S3 CORS Hands On
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















