# AWS Solution Architect Associate (SAA-003)
Link: https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c02/learn/lecture/13541136#overview
## Section 1: Introduction
1.	Video – Course Introduction
2.	Note – You can skip sections 3, 4 & 5 if you have already done practitioner course
3.	Video – Create AWS free tire basic account. 
4.	Note – Help to troubleshoot AWS account activation issue.
5.	Video – About how to use Udemy videos, speed, transcription.
6.	Video – About instructor and how to connect him.

## Section 2: Code and slide download
7.	Note – download course material from below link:
https://courses.datacumulus.com/downloads/certified-solutions-architect-pn9/

## Section 3: Getting started with AWS
8.	AWS overview - Regions and AZ –
Background
-	AWS started internally from 2002. In 2004 they started offering services with SQS service in US and then keep adding services in different countries
-	AWS is market leader as per Gartner’s magic quadrant report
-	AWS enables to build scalable applications.

AWS Global infrastructure:
We can get this info on 
- AWS Regions: Region is cluster of data centres, available all around the world and has name e.g. us-east-2 , eu-east-1
Factors to choose regions(CAPP):
  - Compliance: some data governance and legal requirements
  - Proximity to users: reduce latency.
  - Availibility: All services are not available in all regions.
  - Pricing: Regions are having different pricing. 
-	AWS availability zones (AZs):
Each region has many availability zones (min:2, max:6, usually:3)
Ex: `ap-southeast` has below three AZs:
o	ap-southeast-2a
o	ap-southeast-2b
o	ap-southeast-2c
Each AZ has one or more data centres with redundant power, network, and connectivity.
-	Data centres:
Data centres are separated from each other so they can be safe from disasters.
They are connected with high bandwidth and ultra-low latency networking.  
-	Point of presence:
AWS has 400+ point of presence.
We can check list available service per region using below page:
https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/?p=ngi&loc=4

9.	Console and services
We can search service by name in search bar or explore it by menu navigation alphabetically.
If any service is not available in your region, then change region to find the service

10.	about UI changes in the course – sometime UI may be different in course as AWS has planned changing UI

## Section 4: IAM and AWS CLI
11.	IAM: User, Groups, and policies
Identity and Access Management (IAM) it is a global service(available in all regions).
-	Used to create user and groups.
-	Group can only contain user not a group.
-	We can create user without group but it’s not best practice.
-	User can be part of multiple groups.
-	Always must provide, least needed permissions to the user.

12.	IAM Users a Groups Hands on
- Never use your root account instead create a new administrator account and use that as admin account.	It dangerous to use root users as it has all the services access which may result in heavy cost.
- Create a user by going into IAM service.
-	Set user name. 
-	Assign Group and provide access (only to require resources)
-	Set/generate password, assign tag if any 
-	Download csv with credentials or send email notification to user
-	We can identify IAM user  : Under username menu you will find below info but not for root user
  -	IAM User: super_admin

13.	IAM policies
-	Group policy: If we assign permission policy to a group it is applicable to all the users in that group.
-	Inline policy: We can also assign policy to a user directly
-	Policy Structure consist of:
  -	Policy language version which is date
  -	Identifier id for a policy
  -	Statement:
    -	sid: statement id
    -	effect: Whether statement allows or denies access.
    -	Principal: account/user/role to which this policy applied to
    -	Action: list of actions this policy allows or denies
    -	Resource: list of resources to which the action applied to
    -	Condition: conditions for which this policy is in effect(optional)
## 14.	IAM Policies hands on
- Providing policy access 
  -	Inline (direct to user): 
    -	Go to IAM -> Users -> Select user
    -	Under “add permissions” drop down select “add inline policy” 
    -	Select services, action, resources and set request condition ( MFA/ permission to source IP)
    -	Click on “create policy”  
## 15.	IAM MFA 
-	Password policy 
  -	Set password as per length and complexity of AWS
  -	Set rule to change password after some frequency of time
  -	Not allow to reuse old passwords.
-	MFA
  -	Secure all accounts by setting up physical device or virtual MFA app.
  -	Universal 2nd factor key which is a USB device
  -	RSA token generating device.
## 16.	IAM MFA hands on
-	Click on your account icon on right top and select -> security credentials.
-	Select set MFA and follow steps.
## 17.	AWS access key CLI and SDK
-	Ways to connect to AWS.
  -	AWS management console – protected by  password and MFA.
  -	AWS CLI – uses api and protected by access keys.
  -	AWS SDK – for developer – protected by access keys.
-	Access keys are generated through console. 
-	Users manage their own access keys.
## 18.	AWS CLI setup on windows
-	Download and install AWS CLI V.2 from AWS website for **windows**
-	Check if CLI is install correctly by using the below command in cmd
  -	aws –version
## 19.	Install AWS CLI on MAC OS
-	Download and install AWS CLI V.2 from AWS website for **MAC**
-	Check if CLI is install correctly by using the below command in terminal
    -	aws –version

## 20.	Install AWS CLI on Linux
-	Download CLI 
  - curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
-	Unzip package
  -	unzip awscliv2.zip
-	Install
  -	sudo ./aws/install
-	Check version
  -	aws –version
## 21.	CLI hands on.
-	Generate security keys 
  -	Goto users->select user
  -	Click on -> Create Access keys
  -	Select -> CLI option 
-	Download secret key csv
-	Goto Terminal configure aws 
  -	aws configure
-	There enter access key, password, your preferred region name, and output format hit enter
-	Test aws cli, below command should list all users in json format
  -	aws iam list-users
## 22.	AWS cloudshell: region availability
-	Link provided to check region in which cloudshell is available
  -	https://docs.aws.amazon.com/cloudshell/latest/userguide/faq-list.html#regions-available
## 23.	AWS Cloudshell hands on
-	Cloudshell is just a terminal (CLI) in cloud 
-	It is not available for every region
-	When working on cloudshell your only region would be the one you are selected in AWS management console. however you can change your region while working on local CLI using below command
  -	aws iam list-users –region=us-east-1
-	Where files you create while using cloudshell those will remain stored on cloud
  -	echo “test” > demo.txt
  -	restart cloud shell
  -	you can see demo file there using ls command
-	Using action menu you can
  -	Download / upload file 
  -	Split tab
## 24.	IAM roles for AWS services
-	Some services needs to be performed on our behalf, to do so we will assign permissions to AWS services using IAM roles
-	If we create a EC2 service we may require to give permissions on Roles to EC2 so it can perform task on AWS. There are common roles such as 
  -	EC2 Instance role
  -	Lambda Function role
  -	Roles for cloud formation
## 25.	IAM Roles hands on
-	Goto IAM->roles
-	Select AWS Service-> select service for which role needs to be created
-	Give name to Role
-	Give access by selecting ReadonlyIAMaccess 
## 26.	IAM security tools
-	IAM Credentials Report (account-level)
  -	A report that lists all account’s users and the status of their various credentials.
- IAM Access Advisor (user-level)
  -	Access advisor shows the service permissions granted to a user and when those services were last accessed.
  -	You can use this information to revise your policies.
## 27.	IAM Security Tools Hands on
-	Credentials Report
  -	Click on credentials report from left navigation pane.
  -	Click on download report button.
  -	You will get Credentials Report in CSV format.
-	IAM Access Advisor
  -	Click on Users from Navigation pane of left side.
  -	Select User from by clicking username.
  -	Select “Access Advisor” tab  for service last accessed report.
## 28.	IAM Guidelines & Best Practices
-	Don’t use root account except for AWS account setup.
-	One physical user = One AWS user
-	Assign users to groups and assign permissions to groups.
-	Create strong password policy.
-	Use and enforce the use of MFA.
-	Create and reuse Rules for giving permissions to AWS services.
-	Use Access Keys for Programmatic Access (CLI/SDK).
-	Audit permissions of your account using IAM Credentials Report & IAM Access Advisor.
-	Never share IAM user & Access Keys
## 29.	IAM Summary
-	Users: mapped to a physical user; has a password for AWS Console.
-	Groups: contains users only.
-	Policy: JSON document that outlines permissions for users or groups
-	Roles: for EC2 instance or AWS services
-	Security: MFA + Password policy
-	Access Keys: access AWS using the CLI or SDK
-	Audit: IAM Credential Report & IAM Access Advisor.
