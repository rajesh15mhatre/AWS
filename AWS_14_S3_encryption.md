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










