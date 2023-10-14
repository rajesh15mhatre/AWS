# Section 26: AWS Security& Encryption: KMS, SSM Parameter Store, Shield, WAF

## 292. AWS Security - Section Introduction
## 293. Encryption 101
## 294. AWS KMS
## 295. KMS Hands-On CLI
## 296. KMS Multiregion keys
## 297. S3 Replication with Encryption
## 298. Encrypted AMI Sharing Process
## 299. SSM Parameter Store overview
## 300. SSM Parameter Store Handson (CLI)
- Search service on portal -> System Manager -> and find Parameter Store on navigation
- CLI command to get encrypted parameter values(password was encrypted) 
  - aws ssm get-parameters --names /my-app/dev/db-url /my-app/dev/db-password
- CLI command to get decrypted parameter values (password gets decrypted)
  - aws ssm get-parameters --names /my-app/dev/db-url /my-app/dev/db-password --with-decryption
- To get all parameters under a path
  - aws ssm get-parameters-by-path --path /my-app/dev/ --recursive

## 301. SSM Parameter Store Hands On - Lambda
- We need to attach an inline policy to a role created for lambda, So it can access KMS to decrypt secure strings and Parameter store to get parameter value

## 302. AWS Secrets Manager - Overview
- TO store secrets and its also integrated with RDS and other DBs. It also have multi-region replication

 ## 303. AWS Secrets Managers - Hands On 
 ## 304. AWS Certificate Manager Service (ACM)
 ## 305. Web Application Firewall (WAF)
 ## 306. Shield - DDoS Protection
 DDoS (Distributed Denial of service) Attack protection.
 ## 307. AWS Firewall manager
 ## 308. WAF & Shield - Hands On
 - Search for WAF and Shield 
## **309. DDoS Protection Best Practices** -- **IMP** --
## 310. Amazon GuardDuty
## 311. Amazon Inspector
## 312. Amazon Macie
## Quiz
1. To enable In-flight Encryption (In-Transit Encryption), we need to have ........................
- An HTTP endpoint with SSL certificate
- **An HTTPS endpoint with SSL certificate**
  - In-flight Encryption = HTTPS, and HTTPS can not be enabled without an SSL certificate.
- A TCP endpoint

2. Server-Side Encryption means that the data is sent encrypted to the server.
- FALSE : Server-Side Encryption means the server will encrypt the data for us. We don't need to encrypt it beforehand

3. In Server-Side Encryption, where do the encryption and decryption happen?
- **Both Encryption and Decryption happen on the server**
  - In Server-Side Encryption, we can't do encryption/decryption ourselves as we don't have access to the corresponding encryption key.
- Both Encryption and Decryption happen on the client
- Encryption happens on the server and Decryption on the client
- Encryption on client and decryption on server

4. In Client-Side Encryption, the server must know our encryption scheme before we can upload the data.
-   False - With Client-Side Encryption, the server doesn't need to know any information about the encryption scheme being used, as the server will not perform any encryption or decryption operations.

5. You need to create KMS Keys in AWS KMS before you are able to use the encryption features for EBS, S3, RDS ...
- False - You can use the AWS Managed Service keys in KMS, therefore we don't need to create our own KMS keys.

6. AWS KMS supports both symmetric and asymmetric KMS keys.
- True - KMS keys can be symmetric or asymmetric. A symmetric KMS key represents a 256-bit key used for encryption and decryption. An asymmetric KMS key represents an RSA key pair used for encryption and decryption or signing and verification, but not both. Or it represents an elliptic curve (ECC) key pair used for signing and verification.

7. When you enable Automatic Rotation on your KMS Key, the backing key is rotated every .................
- 90 days
- **1 year**
- 2 years
- 3 years

8. You have an AMI that has an encrypted EBS snapshot using KMS CMK. You want to share this AMI with another AWS account. You have shared the AMI with the desired AWS account, but the other AWS account still can't use it. How would you solve this problem?
- The other AWS account needs to logout and login again to refresh its credential
- **You need to share the KMS CMK used to encrypt the AMI with the other AWS account**
- You can't share an AMI that has an encrypted EBS snapshot

9. You have created a Customer-managed CMK in KMS that you use to encrypt both S3 buckets and EBS snapshots. Your company policy mandates that your encryption keys be rotated every 3 months. What should you do?
- re-configure your KMS CMK and enable Automatic Rotation, in the Period select 3 months
- Use AWS managed KEys as they are automatically rotated by AWS every 3 months
- **Rotate the KMS CMK manually. Create a new KMS CMK and use Key aliases to reference the new KMS CMK. Keep the old KMS CMK so you can decrypt the old data**

10. What should you use to control access to your KMS CMKs?
- **KMS Key Policies**
- KMS IAM Policies
- AWS GuardDuty
- KMS Access COntrol List (KMS ACL)

11. You have a Lambda function used to process some data in the database. You would like to give your Lambda function access to the database password. Which of the following options is the most secure?
- Embed it in the code
- Have it as a plainttext env variable
- **HAve it as a encrypted env  variable and decrypt it runtime**
  - This is the most secure solution amongst these options.
