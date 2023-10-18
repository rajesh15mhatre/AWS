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
- One account = 100 values limit per region
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
