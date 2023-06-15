# Section 6: EC2 -Solutions Architect Level 
## 46 Private vs Public vs Elastic IP
- 2 sort of IPs 
  - IPv4: 1.168.12.123
  - IPv6: 1244:2323:2:200:f4ff:f3er:545Ccv
- IPv4 is the most common format used online
- IPv6 is newer and solves problems for the IoT
- IPv4 allows for 3.7 billion different address in thepublic space
- IPv4: [0-255].[0-255].[0-255].[0-255]
- Company machine connects to www using NAT + internet gateway (a proxy)
- Only a specified range of IPs can be used as private IP
- When you start and stop VM IPV4 changes 
- We can use elastic IP **chargable service** to keep static public IP for VM
- We can have 5 elastic IP in your account (we cal also ask AWS to increase that)
- Avoid using Elastic IP
  - They often reflect poor architecture
  - Instead, use a random public IP and register a DNS name to it 
  - We can also use load balancer
- By default VM comes with Private and Public IP
- Use public IP while SSH
## 47. Private vs Public vs Elastic IP Hands on
- Create VM, connect it by ssh, stop VM , see IP changes , ssh usign old and new IP, attache elastic IP 
connect shh using elastic IP, start stop and see elatic IP is fixed, remove elastic IP 
- Elastic IP are not changable as ling as we don't use it 
## 48 EC2 placement groups
- Control EC2 instance placement groups strategies:
 - Cluster -  cluster instance into a low latency group in a single AZ.
  - Instances are on same racks same AZ
  - Pros: 10 GBPS bandwith
  - Cons: if rack fails on EC2 instacne fails same time
  - Usecase: 
    - Big data jobs that needs to complete fast
    - App that needs extremely low latency and high network throughput
 - Spread - spread instance across underlying hardware (max 7 instances per group per AZ) - critical application
  - Pros: 
    - Can span across AZs
    - Reduced risk of simultaneous failure
  - Con:
    - Limited to 7 instances per AZs per group
  - USe case
    - App that needs to maximize highavailability
    - Critical app where each instance must be isolated from failure from each other
 - Partition  - Spread instance across many different partitions (which rely on different set of racks) within an AZ. Scales to 100 of EC2 instances per group 
 - EC2 instacnes partitions across different racks on an AZ in same region. 
 - In one AZ you can have up to 7 partitions
 - Up to 100s of EC2 instacnes
 - A partition failure can affect many EC2 but won't affect other partitions
 - EC2 instances get access to the pertition information as metadata
 - USecase: Hadoop, Cassandra, Kafka
## 49 EC2 placement group hands on
- Goto EC2 -> polacement group -> provide name -> select strategy (cluster/spared/partition)
- WHile launcing instance -> select placement group

## 50. Elastic network Interfaces
- Helpful in case of failover
- Logical components in a VPC tht represent a virtual network card
- The ENO can have the following attributes:
  - Primary private IPv4, one or more secondary IPv4
  - One Elastic IP(IPv4) per private IPv4
  - One Public IPv4
  - One or more security groups
  - A MAC addesss
- You can have ENI independently and attach them on the fly (move them) on EC2 instances for failover
- Bound to a specific AZ

## 52. Elastic network interfaces (ENI) 
- Launch two EC32 instances
- Goto EC2 instace
- Under  NEtworking goto Network interfaces
- We also have network interface option on navigation
- Create secondar ENI 
- Own created ENI stays enven EC2 is teminated and primary will be deleted 
 
 ## 53. ENI Extra reading 
 - https://aws.amazon.com/blogs/aws/new-elastic-network-interfaces-in-the-virtual-private-cloud/

## 54. EC2 Hibernate
- Incroducing EC2 Hibernate:
  - The in-memory (RAM) state is preserved
  - The instant boot is much faster! (OS is not stopped/ restated)
  - Under the hood RAM state is written to a file on the root of EBS volume
  - The root of EBS volume must be incrypted
- Use Caes for hibernante when EC2 has:
  - Long-running processing 
  - Saving the RAM state
  - Services the take time to initialize 
- EC2 Hibernate Good to know


