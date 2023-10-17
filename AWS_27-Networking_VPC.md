# Section 27: Networking VPC
## 313. Intro
## 314. CIDR, Private vs Public IP
## 315. Default VPC Overview
## 316. VPC overview
## 317. VPC Hands on
## 318. Subnet Overview
## 319. Subnet hands on 
## 320. Internet Gateway & Route Tables 
- internet access to public subnets
## 321. internet Gateway Hands on
## 322. Bastion Hosts
## 323. Bastion Host - hands on 
- SSH to Bashtion host(EC2 on public subnet) then from that SSH to EC2 on private subnet.
## 324. NAT Instances
Network Access translator
## 325. NAT Instance - Hands-on
- Create EC2 instance with community AMI of AMzon Linux having NAT configured. Name it nat-instance
  - Select key pair
  - Create Security Group - nat-instance-sg
    - SHH from anywhere
    - HTTP - TCP - 80
      - source type: custom - source: 10.0.0.0/16 (CIDR)
    - HTTPS - TCP - 443
      - source type: custom - source: 10.0.0.0/16
  - Select Created VPC
  - Select created public subnet
  - Launch
- Pass internet through NAT instance
  - Right-click NAt-Instance and edit networking settings - Change source/destination check
  - And check stop as it's NAT instance bcoz IP changes
- Give internet access to private EC2
  - Connect Bastion Host - SSH to a private instance
  - Goto Route tables - Edit routes
  - Add a rule to send any  IP except local pass it to eni ( NAT instance)
    - DEstiination: 0.0.0.0/0 (ineternewt any ip)
    - Target ( Select NAT Instance)
    - Save
- Enable the Port of NAT
  - GOto NAT instance - security tab - goto attached security group 
  - Edit the inbound rule
    - Add-Type: All ICMO -IPv4
    - Source : 10.0.0.0/16 (CIDR)
    - Save 
 - Check internet settings on Private EC2 instance
- Terminate NAT Instance
## 326. NAT Gateways
## 327. NAT Gateways - hands on
- Create NAT gateway to connect to internet from private EC2
  - Create NAT 
    - goto VPC - NAT Gateway - name it - DemoNATGW
    - select public subnet 
    - Connectivity type - public
    - Allocate elastic IP
    - Create
- Set Private route
  - Right-click edit routes
  - DEstiination: 0.0.0.0/0 (ineternewt any ip)
  - Target ( Select NAT Gateway)
  - Save
- Wait for NAT GW to activate and the Internet will be active
- Create multiple NAT GW for HA
## 328. NACLs & Security Groups
- stateful and stateless
  - SG is stateful - meaning if any inbound  is allowed in SG then the same will not be evaluated on outbound so that would be allowed  in SG. The same goes for outbound evaluated requests.  
  - NACL ( Network Access Control List) is stateless: meaning any request outbound or inbound gets evaluated on NACL their earlier inbound and outbound evaluation doesn't get considered 
- NACL Are subnet level 1 per subnet 
- Default NACLs accept everything inbound/outbound with the subnet it's associated with
## 329. NACL & Security Groups Hands-on
## 330. VPC Peering
## 331. VPC Peering Hands-on
## 332. VPC Endpoints
- It helps Connect AWS services privately without using the internet 
## 333. VPC Endpoints hands-on
## 334. VPC Flow Logs
## 335. VPC Flow Logs - Hands-on
## 336. Stire to Site VPN, Virtual Private Gateway & customer Gateway 
## 337. Stire to Site VPN, Virtual Private Gateway & customer Gateway - Hands-on
## 338. Direct Connect & Direct Connect Gateway
## 339. Direct Connect and Site to Site VPN
## 340. Transit Gateway
## 341. VPC Traffic Mirroring
## 342. IPV6 For VPC
## 343. IPV6 For VPC - Hands-on
## 344. Egress Only Internet Gateway
- We can connect the public subnet with IPV4 /6 to the internet via IGW
- For private subnet, we connect to the internet for IPV4 via NateGW->IGW and for IPV6 via Igress directly.
## Section Clean up
- Delete below services
  - Terminate all EC2s
  - Elastic IPs
  - Egress gateway
  - Internet gateway
  - Route table
  - Subnet
  - VPC Endpoints
  - NAT Gateways - release Elastic IPs
  - Peering connection
  - Network ACLs
  - VPCs
- Check the billing page for any services used
## 347. VPC Section summary
## 348. Networking cost in AWS
- Sharing S3 data over CLoudFront is cheaper
- Accessing S3 data over the internet via NAT GW-> IGW is costlier than accessing it over a private connection via a VPC endpoint
## 349. AWS Network Firewall - VPC level
## Quiz
1. What does this CIDR 10.0.4.0/28 correspond to?
- **10.0.4.0 to 10.0.4.15**
  - /28 means 16 IPs (=2^(32-28) = 2^4), means only the last digit can change.
- 10.0.4.0 to 10.0.32.0
- 10.0.4.0 to 10.0.4.28
- 10.0.4.0 to 10.0.16.0

2.You have a corporate network of size 10.0.0.0/8 and a satellite office of size 192.168.0.0/16. Which CIDR is acceptable for your AWS VPC if you plan on connecting your networks later on?
- 172.16.0.0/12
- **172.16.0.0/16**
  - CIDR not should overlap, and the max CIDR size in AWS is /16.
- 10.0.16.0/16
- 192.68.4.0/18

3. You plan on creating a subnet and want it to have at least capacity for 28 EC2 instances. What's the minimum size you need to have for your subnet?
- /28
- /27
- **/26**
  - Perfect size, 64 IPs. As 5 IPS are reserved by AWS we can't go for /27 which is 32. bcoz 32-5 = 27 which is < 28
- /25

4. Security Groups operate at the ................. level while NACLs operate at the ................. level.
- **EC2 instance, subnet**
- Subnet, EC2 Instance

5. You have attached an Internet Gateway to your VPC, but your EC2 instances still don't have access to the Internet. What is **NOT** a possible issue?
- Route Tables are missing entries
- The EC2 instances don't have public IPs
- **The Security Groups does not allow traffic in**
  - Security groups are stateful and if traffic can go out, then it can go back in.
- The NACL does not allow network traffic out

6. You would like to provide Internet access to your EC2 instances in private subnets with IPv4 while making sure this solution requires the least amount of administration and scales seamlessly. What should you use?
- NAT Instance with Source/Destination Check flag off
- Egress-only Internet Gateway
- **NAT Gateway**

  7. VPC Peering has been enabled between VPC A and VPC B, and the route tables have been updated for VPC A. But, the EC2 instances cannot communicate. What is the likely issue?
- Check the NACL
- **Check the Route Tables in VPC B**
  - Route tables must be updated in both VPCs that are peered.
- Check the EC2 instance attached Security Groups
- Check if DNS Resolution is enabled

8. You have set up a Direct Connect connection between your corporate data center and your VPC A in your AWS account. You need to access VPC B in another AWS region from your corporate data center as well. What should you do?
- Enable VPC peering
- Use a Customer Gateway
- **Use a Direct Connect Gateway**
  - This is the main use case of Direct Connect Gateways.
- Set up a NAT Gateway

9. When using VPC Endpoints, what are the only two AWS services that have a Gateway Endpoint available?
   - When using VPC Endpoints, what are the only two AWS services that have a Gateway Endpoint available?
   - S3 & SQS
   - SQS & DynamoDB
   - **S3 & DynamoDB**
     - These two services have a VPC Gateway Endpoint (remember it), all the other ones have an Interface endpoint (powered by Private Link  which means a private IP).

10. AWS reserves 5 IP addresses each time you create a new subnet in a VPC. When you create a subnet with CIDR 10.0.0.0/24, the following IP addresses are reserved, EXCEPT ....................
- 10.0.0.1
- 10.0.0.2
- 10.0.0.3
- **10.0.0.4**

11. You have 3 VPCs A, B, and C. You want to establish a VPC Peering connection between all the 3 VPCs. What should you do?
- As VPC peering supports Transitive Peering, you need to establish 2 VPC peering connections (A-B, B-C)
- **Establish VPC Peering connections (A-B, B-C, A-C)**

12. How can you capture information about IP traffic inside your VPCs?
- **Enabled VPC Flow Logs**
  - VPC Flow Logs is a VPC feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC.
- Enable VPC Traffic Mirroring
- Enable CLoudWatch Traffic Logs

13. If you want a 500 Mbps Direct Connect connection between your corporate datacenter to AWS, you would choose a .................. connection.
- Dedicated
- **Hosted**
  - Hosted Direct Connect connection supports 50Mbps, 500Mbps, up to 10Gbps.

14. When you set up an AWS Site-to-Site VPN connection between your corporate on-premises data center and VPCs in AWS Cloud, what are the two major components you want to configure for this connection?
- Customer Gateway and NAT Gateway
- Internet Gateway and Custome Gateway
- Virtual Private Gateway and Intenet Gateway
- **Virtual Private Gateway and Customer Gateway**

15. Your company has several on-premises sites across the USA. These sites are currently linked using private connections, but your private connections provider has been recently quite unstable, making your IT architecture partially offline. You would like to create a backup connection that will use the public Internet to link your on-premises sites, that you can failover in case of issues with your provider. What do you recommend?
- VPC Peering
- **AWS VPN CloudHub**
  - AWS VPN CloudHub allows you to securely communicate with multiple sites using AWS VPN. It operates on a simple hub-and-spoke model that you can use with or without a VPC.
- Direct Connect
- AWS PrivateLInk

16. You need to set up a dedicated connection between your on-premises corporate data center and AWS Cloud. This connection must be private, and consistent, and traffic must not travel through the Internet. Which AWS service should you use?
- Site-to-site VPN
- AWS Private link
- **AWS Direct Connect**
- Amazon EventBridge


17. Using a Direct Connect connection, you can access both public and private AWS resources.
- **True**

18. You want to scale up an AWS Site-to-Site VPN connection throughput, established between your on-premises data and AWS Cloud, beyond a single IPsec tunnel's maximum limit of 1.25 Gbps. What should you do?
- Use 2 Virtual Private Gateway
- Use Direct Connect Gateway
- **Use Transit Gateway**


18. You have a VPC in your AWS account that runs in a dual-stack mode. You are continuously trying to launch an EC2 instance, but it fails. After further investigation, you have found that you are no longer have IPv4 addresses available. What should you do?
- Modify your VPC to run in IPV6 mode only
- Modify your VPC to run in IPv4 mode only
- **Add an additional IPv4 CIDR to your VPC**


19. A web application backend is hosted on EC2 instances in private subnets fronted by an Application Load Balancer in public subnets. There is a requirement to give some of the developers access to the backend EC2 instances but without exposing the backend EC2 instances to the Internet. You have created a bastion host EC2 instance in the public subnet and configured the backend EC2 instances Security Group to allow traffic from the bastion host. Which of the following is the best configuration for bastion host Security Group to make it secure?
- Allow traffic only on port 80 from the company's public CIDR
- **Allow traffic only on port 22 from the company's public CIDR**
- Allow traffic only on port 22 from the company's private CIDR
- Allow traffic only on port 80 from the company's private CIDR


20. A company has set up a Direct Connect connection between their corporate data center to AWS. There is a requirement to prepare a cost-effective secure backup connection in case there are issues with this Direct Connect connection. What is the most cost effective and secure solution you recommend?
- Setup another Direct Connect connection to the same AWS region
- Setup another Direct Connect connection to a different AWS region
- **Setup a Site-to-Site VPN connection as a backup**

21. Which AWS service allows you to protect and control traffic in your VPC from layer 3 to layer 7?
- **AWS Network Firewall**
- Amazon Guard Duty
- Amazon Inspector
- Amazon Shield

23. A web application hosted on a fleet of EC2 instances managed by an Auto Scaling Group. You are exposing this application through an Application Load Balancer. Both the EC2 instances and the ALB are deployed on a VPC with the following CIDR 192.168.0.0/18. How do you configure the EC2 instances' security group to ensure only the ALB can access them on port 80?
- Add an inbound Rule with port 80 and 0.0.0/0 as the source
- Add an Inbound Rule with port 80 and 192/168.0.0./18 as the source
- **Add an Inbound Rule with port 80 and ALB's Security Group as the source**
  - This is the most secure way of ensuring only the ALB can access the EC2 instances. Referencing by security groups in rules is an extremely powerful rule and many questions at the exam rely on it. Make sure you fully master the concepts behind it!
- Load an SSL certificate on the ALB
