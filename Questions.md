1. large file uploads quickly to s3
  - S3 Transfer Acceleration with multipart uploads for large file uploads quickly
2 simple on demand query on S3 files with min changes and less overhead?
  - Use Amazon Athena directly with Amazon S3 to run the queries as needed
3. limit access to s3 bucket to only users of accounts within the AWS organization
- Add the aws PrincipalOrgID global condition key with a reference to the organization ID to the S3 bucket policy.
4. provide s3 access to  EC2 instance in a VPC without internet?
- Create a gateway VPC endpoint to the S3 bucket.

5. 2 Ec2, each with EBS, uswer report they don't see full data

A. Wrong-  Copying data to both EBS will be duplicating storage
B. Wrong - this can be possible with stiky session  Configure the Application Load Balancer to direct a user to the server with the documents
C. Copy the data from both EBS volumes to Amazon EFS. Modify the application to save new documents to Amazon EFS
D. Configure the Application Load Balancer to send the request to both servers. Return each document from the correct server
