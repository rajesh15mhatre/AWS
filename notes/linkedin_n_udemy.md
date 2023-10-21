# Storage
## 1. S3
- Uses REST API
- Classes : Standard, Infrequent Access(IA), Reduced Redundancy(RRS), Glacier
- Lifecycle Management policies - can use tags and prefixes
  - Used for Intelligent Tiering
- Encryption: SSE, CSE
- Versioning-Default disabled
- MFA Delete
- Multi-part upload
- Range GETs - Fetch part of large file
- Cross-region replication: only for new files
- Logging
- Event notification
- Bucket name globally unique
- Object can have max 10 tags
- Object locking: WORM ( Write once Reads many)
## 2. Glacier
- objects called as archives
- Buckets called vaults, and vault locks
- no charge for up to 5% data retrieval per month
- One account = 1000 values limit per region.
- Only empty vaults can be deleted
- Supports multi-part upload
- Batch Operation: Act on objects
## 3. Storage gateway
- Types: file gateway (s3), Volume gateway (EBS), Tape Gateway (Tape storage)
- Virtual Tape Library for tapes
## 4. EBS
- TYpes: Magnetic(cheap), SSD(GP, Provisioned IOPS, EBS Optimized instance)
- Snapshots for backup, Duplicate instance
- Volume Recovery ( Attaching from other instances in the same AZ)
- supports Encryption
## 5. EFS
- It's a file shared on the network through NFSv4
- Only for Linux and limited to VPC
- Workaround for Windows: We can create a shared folder on the Windows instance with EBS for other instances to share data 
- Use MaxI/O for read/write heavy workload
- Brusting throughput means keeping it lowest and escalating if needed
- Endpoints are under VPC
- Private links allow secure connection between VPCs, Services, and apps in AWS
## 6. FSx
- It fully manages shared services -like building AD and server
- Supports SMB - windows and Lustre for Linux
-  Can be deployed in one or many AZs
-  AD can be self-managed or by AWS
## Storage  gateway 
- On-prem storage to the cloud and vice versa
- via Vmware-ESX or Hyperwave
- Its software image
- types:
  - File-Based - NTFS
  - Volume Base - Internet SCSI protocol over IP
  - Tape-based - Virtual Tape
- Hosting on EC2 or on-prem
## Security 
- user policy
- GigaByte(10^3...) and GigiByte (2^10...) are different

# Security
## IAM 
- IAM Credentials Report - account's users and the status of their various
credential
- IAM Access Advisor - User level report, Service permission and last accessed date. Used to revise your policies
- MFA and strong Password policy
- 
## VPC
- New account has default VPC
  - Default VPC Features
    - Dynamic Private/public IP
    - AWS provisioned/Private/public DNS Names
- Amazon recommends not deleting existing \default VPC
- DHCP: Dynamic Host Configuration Protocol used to assign IP address automatically to network devices
  - You can configure the following parameters in DHCP options: Domain Name Servers, Domain Name, NTP Servers, NetBIOS name servers, and NetBIOS node types. 
- EIP: Elastic IP is chargeble
  - can be moved between EC2 in the same region
  - ENI uses Elastic IPs
- DMZ: A demilitarized zone network provides a buffer between the internet and an organization's private network. The DMZ is isolated by a security gateway, such as a firewall.
- ENIs
  - Allows dual-homing: Multiple ENIs attached to an instance achieve dual homing which is access to private as well as  public address
  - It's a virtual network card
  - It's associated with a subnet
- Endpoints
  - Are channels to connect AWS services that are out of VPC
## VPC Peering
- Connects one VPC to another. In legacy on-prem systems we may have created a WAN connection between two companies' private connections and would have given federated access based on AD
- Process:
  - Initiating VPC sens request to the receiving VPC
  - VPC owners only can perform peering
  - IP CIDR must not overlap
    - All VPCs should have unique CIDR
  - Require Route table and security group modifications
## VPC Security
- Security Groups
  - Act like firewall assigned to an Instance
  - Defines allowed ingress(enterance) or Egress(exit) traffic flows
  - Its closed to open system, default everything is closed and we allows as we need
  - It's stateful what allowed inbound also allowed outbound and vice versa
- NACLs
  - APplied on subnets
  - Stateless - need both allow and deny rule
  - Rule number define precedence. First match applies
  - like router solutions or firewall at netwok
- NATs
  - Translates single external address to multiple internal address
  - Supports public (where external address is public) and private (where both addresses are private) implementation
- VPG - implemented in cloud
  - Connects local networks to the VPC
  - VPG is the VPN concentrator (multiple VPC can be accessed)
- CGW - implemented in the customer network
  - Physical device or software app
  - Anchore on the customer side
    - Connects to VPG
- Both CGW and VPG together form VPN
- Alternatives:
  - AWS hardware VPN
  - AWS Direct Connect
  - VPN Cloud Hub
  - Software VPN (L2TP and iPsec)
- VPN Configuration Options
  - Split tunnel support was introduced in 2019
    - Gives flexibility for routing traffic across the VPN specifically for traffic going out to the Internet. Ex. allows internet traffic to by PASS VPN and use VPN for AWS cloud traffic only
  - Certificate enabled to authenticate VPN
  - Direct connect: Bypasses traditional ISP and connects straight into AWS
    





















