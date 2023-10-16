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
## 339. Direct Connect and Site to SIte VPN
## 340. Transit Gateway
## 341. VPC Traffic Mirroring
## 342. IPV6 For VPC
## 343. IPV6 For VPC - Hands-on
## 344. Egress Only Internet Gateway


