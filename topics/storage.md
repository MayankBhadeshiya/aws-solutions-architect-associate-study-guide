# AWS Storage Services

## Overview
AWS offers a comprehensive suite of storage services designed to provide secure, scalable, and durable storage for various application needs. This guide covers key AWS storage services with a focus on architectural considerations for the Solutions Architect Associate exam.

## Storage Categories

AWS storage services fall into three main categories:

| Category | Characteristics | AWS Services | Common Use Cases |
|----------|-----------------|--------------|------------------|
| Block Storage | Fixed-size data blocks, low latency, direct access | EBS, Instance Store | Databases, boot volumes, applications requiring consistent I/O |
| File Storage | Hierarchical structure, shared access | EFS, FSx | Shared content, data sharing across instances, content management |
| Object Storage | Flat structure, unlimited scale, HTTP access | S3, S3 Glacier | Unstructured data, backups, archives, static assets, data lakes |

### Understanding Storage Types

#### Block Storage
Block storage splits files into fixed-size blocks of data, each with its own address. When data is requested, these addresses allow the system to retrieve and assemble blocks efficiently. When you want to change a character in a file, you only need to update the specific block containing that character, making operations fast with low bandwidth usage.

**Characteristics:**
- Blocks are managed by the operating system as mounted volumes
- Excellent for random read/write operations
- Low-latency performance
- Similar to traditional direct-attached storage (DAS) or storage area networks (SAN)
- In AWS: EBS volumes, EC2 Instance Store

#### File Storage
File storage organizes files in a hierarchical structure of folders and subfolders, similar to how you organize files on your computer. Each file includes metadata such as file name, creation date, and path information.

**Characteristics:**
- Files are accessible through file system interfaces (path-based)
- Easily shared across multiple clients
- Familiar organization system (folders/subfolders)
- Similar to traditional network-attached storage (NAS)
- In AWS: Amazon EFS, Amazon FSx

#### Object Storage
Object storage treats each file as a complete object stored in a flat structure. Each object has a unique identifier and can include metadata. To modify an object, you must replace the entire object, as you cannot modify just a portion.

**Characteristics:**
- Infinitely scalable
- Access via HTTP/HTTPS (REST APIs)
- Rich metadata capabilities
- Ideal for static content
- In AWS: Amazon S3, S3 Glacier

## Instance Store Volumes

### What is EC2 Instance Store?
Instance Store provides temporary block-level storage that is physically attached to the host computer running your EC2 instance. This storage is ephemeral, meaning its lifecycle is tied directly to the EC2 instance - if you delete or stop the instance, all data in the instance store is lost. However, data persists during reboots.

### Characteristics
- Physically attached to host server
- Highest I/O performance in AWS
- Ephemeral storage (data lost on stop/terminate)
- Included in instance price
- Size depends on instance type
- Cannot be detached/reattached

### Use Cases
- Temporary data (caches, buffers)
- Replicated data (distributed databases)
- High-performance scratch space
- Stateless applications
- Applications that replicate data across multiple EC2 instances (e.g., Hadoop clusters)
- Temporary processing of high-volume data

### Architectural Considerations
- Data persists during reboot, but not stop/terminate
- Size and performance fixed by instance type
- No snapshots capability
- Consider data replication for durability
- Must be set up at instance launch time
- No additional cost (included in instance price)

## Amazon EBS (Elastic Block Store)

### What is Amazon EBS?
Amazon EBS provides persistent block-level storage volumes that you can attach to EC2 instances. Similar to attaching an external hard drive to your computer, EBS volumes are separate from the instance and persist independently of the instance lifecycle. This means you can terminate an instance and still keep your data, or detach a volume from one instance and attach it to another.

### Volume Types
| Volume Type | Description | IOPS | Throughput | Use Cases |
|-------------|-------------|------|------------|-----------|
| gp3 | General Purpose SSD | 3K-16K | 125-1000 MB/s | Boot volumes, dev environments, low-latency apps |
| gp2 | General Purpose SSD | 100-16K | 128-250 MB/s | Boot volumes, dev environments |
| io2 Block Express | Provisioned IOPS SSD | Up to 256K | Up to 4,000 MB/s | Critical, high-performance databases |
| io2 | Provisioned IOPS SSD | Up to 64K | Up to 1,000 MB/s | I/O intensive databases, mission-critical workloads |
| io1 | Provisioned IOPS SSD | Up to 64K | Up to 1,000 MB/s | I/O intensive databases (legacy) |
| st1 | Throughput Optimized HDD | Up to 500 | Up to 500 MB/s | Big data, data warehousing, log processing |
| sc1 | Cold HDD | Up to 250 | Up to 250 MB/s | Cold data, infrequently accessed workloads |

### EBS Features
- **Snapshots**: Point-in-time backups of volumes stored in S3
- **Encryption**: AES-256 encryption using KMS
- **Multi-Attach**: Attach io1/io2 volumes to multiple instances simultaneously
- **Fast Snapshots**: Faster snapshot creation and restoration
- **Elastic Volumes**: Dynamically modify volume type, size, and performance
- **Data Lifecycle Manager**: Automate snapshot and AMI management
- **EBS direct APIs**: Direct access to EBS snapshot data

### EBS Snapshots Explained
EBS snapshots are incremental backups that save only the blocks that have changed since your last snapshot. For example, if you have 10 GB of data on a volume and only 2 GB have changed since your last snapshot, only the 2 GB of modified data are written to S3.

Benefits of snapshots:
- Space-efficient storage (incremental)
- Stored redundantly across multiple Availability Zones using S3
- Can be used to create new volumes in any AZ
- Serve as baseline for new volumes
- Can be copied across regions for disaster recovery
- Can be shared with other AWS accounts

### Architectural Considerations
- Regional service (AZ-specific)
- Size limits: 1GB to 64TB depending on volume type
- 1:1 attachment ratio (except for multi-attach io1/io2)
- Performance limited by volume and instance type
- Provisioned IOPS for predictable performance
- Use RAID configurations for higher IOPS/throughput
- Backup strategy with cross-region snapshots
- EBS volumes can be scaled in two ways:
  - Increase volume size (up to 16TB)
  - Attach multiple volumes to a single EC2 instance

## Amazon S3 (Simple Storage Service)

### What is Amazon S3?
Amazon S3 is an object storage service designed to store and retrieve any amount of data from anywhere on the web. Unlike EBS, S3 is a standalone storage solution not attached to compute resources. S3 stores data as objects within containers called buckets with virtually unlimited storage capacity.

### Core Concepts
- Object storage service for any amount of data
- 99.999999999% durability (11 nines)
- Unlimited storage capacity
- Objects up to 5TB in size
- Bucket names must be globally unique
- Regional service with global namespace
- Objects are identified by a URL (e.g., https://bucket-name.s3.amazonaws.com/object-key)

### S3 Bucket Basics
When creating an S3 bucket, you must specify:
1. A globally unique bucket name
2. The AWS Region where the bucket will reside

Objects in buckets:
- Consists of data, key (name), and metadata
- Can be organized using prefixes to simulate a folder structure
- Have a flat structure (no actual file hierarchy)

### Storage Classes
| Storage Class | Availability | Retrieval | Min. Storage Duration | Use Cases |
|---------------|-------------|-----------|----------------------|-----------|
| S3 Standard | 99.99% | Immediate | None | Frequently accessed data, websites |
| S3 Intelligent-Tiering | 99.9% | Immediate | None | Unknown or changing access patterns |
| S3 Standard-IA | 99.9% | Immediate | 30 days | Long-lived, infrequently accessed data |
| S3 One Zone-IA | 99.5% | Immediate | 30 days | Non-critical, replaceable infrequently accessed data |
| S3 Glacier Instant Retrieval | 99.9% | Milliseconds | 90 days | Archival data needing immediate access |
| S3 Glacier Flexible Retrieval | 99.9% | Minutes to hours | 90 days | Archival data with flexible retrieval |
| S3 Glacier Deep Archive | 99.9% | Hours | 180 days | Long-term retention, rarely accessed data |

### S3 Versioning
Versioning preserves multiple variants of an object in the same bucket, allowing you to restore previous versions and recover from accidental deletions or overwrites.

Key versioning features:
- Stores all versions of an object, including writes and deletes
- Each version has a unique version ID
- Once enabled, versioning cannot be disabled (only suspended)
- Deletion places a "delete marker" instead of permanently removing the object
- Previous versions can be restored by removing the delete marker

Versioning states:
- Unversioned (default): No versioning
- Versioning-enabled: All objects versioned
- Versioning-suspended: New objects unversioned, existing objects retain versions

### S3 Features
- **Versioning**: Preserve multiple versions of objects
- **Lifecycle Management**: Automate transitions between storage classes
- **Encryption**: SSE-S3, SSE-KMS, SSE-C, client-side encryption
- **Object Lock**: WORM (Write Once Read Many) capability
- **Access Points**: Customized access to shared datasets
- **Multi-Region Access Points**: Global endpoints for multi-region access
- **Replication**: Cross-region and same-region replication
- **Event Notifications**: Trigger workflows on object operations
- **Requester Pays**: Transfer costs paid by requester instead of bucket owner

### S3 Security
- **IAM Policies**: Identity-based access control
- **Bucket Policies**: Resource-based access control at the bucket level
- **ACLs**: Legacy object-level permissions (not recommended for new implementations)
- **Block Public Access**: Settings to prevent public access
- **Access Points**: Simplified access management for shared datasets
- **S3 Object Lambda**: Transform data as it's retrieved
- **VPC Endpoints**: Private access from VPCs without internet

#### IAM Policies vs. Bucket Policies
**IAM Policies:**
- Attached to IAM users, groups, or roles
- Define which S3 actions the identity can perform
- Used when managing many buckets with different permissions
- Centralized management in IAM

**Bucket Policies:**
- JSON documents attached directly to S3 buckets
- Specify what actions are allowed/denied on the bucket
- Apply to all objects within the bucket
- Can grant cross-account access without IAM roles
- Larger size limit than IAM policies

Example Bucket Policy (Public Read):
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": ["s3:GetObject"],
            "Resource": ["arn:aws:s3:::example-bucket/*"]
        }
    ]
}
```

### Advanced S3 Features
- **S3 Select**: SQL queries on objects to retrieve subsets of data
- **S3 Batch Operations**: Perform operations on large numbers of objects
- **Transfer Acceleration**: Fast, secure transfers using CloudFront
- **Inventory**: Reports on objects and metadata
- **Storage Class Analysis**: Recommendations for lifecycle policies
- **S3 Object Lambda**: Transform objects during retrieval
- **Directory Buckets**: High-performance, strongly consistent storage

## Amazon EFS (Elastic File System)

### Performance Modes
- **General Purpose**: Low latency, suitable for most workloads
- **Max I/O**: Higher latency, higher throughput, parallel workloads

### Throughput Modes
- **Bursting**: Throughput scales with storage size
- **Provisioned**: Set specific throughput independent of storage size
- **Elastic**: Automatically scales throughput up and down based on workload

### Storage Classes
- **Standard**: Frequently accessed data
- **Infrequent Access (IA)**: Lower cost for infrequently accessed data
- **Archive**: Lowest cost for rarely accessed data

### Advanced Features
- **Lifecycle Management**: Automatically move files between classes
- **One Zone Storage**: Lower cost, single AZ option
- **Access Points**: Application-specific entry points with permissions
- **Replication**: Automatic cross-region replication
- **Encryption**: Encryption at rest and in transit

### Architectural Considerations
- Regional service with multi-AZ resilience (except for One Zone)
- Elastic storage capacity (PB scale)
- Linux-only (NFS v4.1)
- Thousands of concurrent connections
- Shared access across instances and Lambda functions
- Higher cost per GB than EBS, but pay for used storage
- Use EFS access points for application isolation

## Amazon FSx Family

### FSx for Windows File Server
- **Features**: SMB protocol, Windows NTFS, DFS namespaces
- **Integration**: Active Directory, ACLs, VSS
- **Performance**: Up to 3 GB/s throughput, hundreds of thousands of IOPS
- **Use Cases**: Home directories, file shares, Windows applications

### FSx for Lustre
- **Features**: High-performance parallel file system
- **Performance**: Up to hundreds of GB/s, millions of IOPS
- **Integration**: S3 integration, data repositories
- **Use Cases**: HPC, machine learning, media processing, financial modeling

### FSx for NetApp ONTAP
- **Features**: Multi-protocol access (NFS, SMB, iSCSI)
- **Capabilities**: Snapshots, replication, compression, deduplication
- **Use Cases**: VMware environments, hybrid storage architectures

### FSx for OpenZFS
- **Features**: NFS v3, v4, v4.1, v4.2
- **Capabilities**: Snapshots, compression, ZFS capabilities
- **Use Cases**: Linux workloads, data analytics, application migrations

### Architectural Considerations
- Regional services with Single-AZ or Multi-AZ deployment options
- Storage capacity and performance can be scaled independently
- Backup and maintenance management
- Consider throughput requirements when selecting deployment type
- Network performance impacts file system performance

## Amazon S3 Glacier

### Glacier Storage Classes
| Storage Class | Retrieval Times | Min. Storage Duration | Retrieval Fees |
|---------------|-----------------|----------------------|----------------|
| Glacier Instant Retrieval | Milliseconds | 90 days | Lower |
| Glacier Flexible Retrieval | Minutes to hours | 90 days | Variable |
| Glacier Deep Archive | Hours (12h) | 180 days | Lowest |

### Retrieval Options for Flexible Retrieval
- **Expedited**: 1-5 minutes, highest cost
- **Standard**: 3-5 hours, standard cost
- **Bulk**: 5-12 hours, lowest cost

### Features
- Vaults and archives for organizational structure
- Vault Lock for WORM (Write Once Read Many) compliance
- Vault access policies for access control
- Inventory retrieval for listing archives
- Range retrievals for partial file access

### Architectural Considerations
- Plan for retrieval costs and times
- Consider Capacity Reservations for Expedited retrievals
- Store metadata separately for easier object identification
- Use lifecycle policies to automate transitions
- Design for the 90-day or 180-day minimum storage duration

## AWS Storage Gateway

### Types of Storage Gateway
| Gateway Type | Function | Use Cases |
|--------------|----------|-----------|
| S3 File Gateway | NFS/SMB access to S3 | File share, S3 integration |
| FSx File Gateway | Local cache for FSx for Windows | Branch office, remote file access |
| Volume Gateway (Cached) | iSCSI volumes with S3 backend and local cache | Extending on-premises storage, backups |
| Volume Gateway (Stored) | iSCSI volumes with S3 backend | Storage migration, disaster recovery |
| Tape Gateway | Virtual tape library with S3/Glacier backend | Backup software integration, tape replacement |

### Deployment Options
- Hardware appliance
- VMware ESXi
- Microsoft Hyper-V
- Linux KVM
- Amazon EC2

### Features
- Local caching
- Bandwidth throttling
- Scheduled snapshots
- Storage volume encryption
- Compression

### Architectural Considerations
- Local network performance impacts throughput
- Cache size planning based on working dataset
- Bandwidth requirements for initial seeding and ongoing replication
- High availability configuration
- Recovery point objective (RPO) planning

## AWS Snow Family

### Devices
| Device | Storage Capacity | Compute Capability | Data Transfer |
|--------|------------------|-------------------|---------------|
| Snowcone | 8TB | 2 vCPUs, 4GB memory | Online or offline |
| Snowball Edge Storage Optimized | 80TB | Up to 40 vCPUs, 80GB memory | Offline |
| Snowball Edge Compute Optimized | 42TB | Up to 52 vCPUs, 208GB memory | Offline |
| Snowmobile | Up to 100PB | N/A | Offline |

### Features
- **Edge computing**: Run EC2 and Lambda at the edge
- **Encryption**: 256-bit encryption of data
- **Physical security**: Tamper-evident enclosures, TPM
- **OpsHub**: Graphical management interface
- **Snow Family Devices for offline data transfer**

### Use Cases
- Large-scale data migrations
- Datacenter decommission
- Disaster recovery
- Edge computing in remote locations
- Content distribution to disconnected environments

### Architectural Considerations
- Lead time for device delivery (typically 1-2 weeks)
- Data transfer speed limited by local environment
- Plan for parallel device ordering for large migrations
- Consider Snowball vs. Snowmobile breakpoint (approximately 10PB)
- Network connectivity for edge computing scenarios

## AWS Transfer Family

### Services
- **AWS Transfer for SFTP**: Fully managed SFTP service
- **AWS Transfer for FTPS**: Fully managed FTPS service
- **AWS Transfer for FTP**: Fully managed FTP service

### Features
- Managed file transfer protocols (FTP, FTPS, SFTP)
- Integration with S3 and EFS as storage backend
- Custom domain support
- Authentication options (Service-managed, AWS Directory Service, Custom)
- CloudWatch metrics and logging

### Architectural Considerations
- VPC endpoint support for private network access
- High availability with no additional configuration
- Consider security implications of chosen protocol
- Cost model based on endpoint hours and data transfer
- Identity provider integration for user management

## Common Exam Scenarios

### Data Lake Implementation
**Scenario**: Design a data lake solution for petabyte-scale analytics.
**Solution**:
- S3 for primary storage (Standard for hot data, Intelligent-Tiering for variable access)
- S3 Glacier for archival results
- S3 Storage Class Analysis to optimize class transitions
- Lifecycle policies for automated management
- AWS Lake Formation for governance
- Athena/EMR/Redshift Spectrum for analytics

### Hybrid Storage Architecture
**Scenario**: Design a solution for an organization with both cloud and on-premises applications that need shared access to data.
**Solution**:
- Storage Gateway for on-premises applications
- S3 for cloud-native storage
- Direct Connect for reliable connectivity
- Consider caching strategy based on access patterns
- Implement consistent backup strategy across environments

### High-Performance Computing Storage
**Scenario**: Design storage for an HPC workload requiring high throughput and low latency.
**Solution**:
- FSx for Lustre with high-performance mode
- S3 integration for persistent storage of results
- Instance store volumes for highest performance scratch space
- EBS Provisioned IOPS for application data
- Consider data placement and instance selection for optimal network performance

### Backup and Disaster Recovery
**Scenario**: Design a comprehensive backup and DR strategy for enterprise workloads.
**Solution**:
- EBS snapshots for point-in-time recovery
- S3 cross-region replication for regional resilience
- AWS Backup for centralized management
- Glacier Deep Archive for long-term retention
- Consider RPO/RTO requirements when selecting services

## Storage Service Selection Guide

### Decision Framework
- **Data characteristics**: Size, type, structure, access patterns
- **Performance requirements**: IOPS, throughput, latency
- **Durability and availability**: SLAs, redundancy needs
- **Sharing requirements**: Single instance vs. multi-instance access
- **Integration**: With existing applications and workflows
- **Cost considerations**: Usage patterns, lifecycle management

### Block Storage Selection
| Requirement | Recommended Service |
|-------------|---------------------|
| Boot volumes | EBS gp3 |
| Database workloads (high performance) | EBS io2/io2 Block Express |
| Database workloads (standard) | EBS gp3 |
| Big data processing | EBS st1 or Instance Store |
| Low-cost infrequent access | EBS sc1 |
| Highest possible performance | Instance Store |
| Shared block storage | EBS Multi-Attach (limited cases) |

### File Storage Selection
| Requirement | Recommended Service |
|-------------|---------------------|
| Linux applications, shared access | EFS |
| Windows applications | FSx for Windows File Server |
| High-performance computing | FSx for Lustre |
| Multi-protocol access | FSx for NetApp ONTAP |
| Traditional application migration | FSx for OpenZFS |
| Cost-optimized access patterns | EFS with Lifecycle Management |

### Object Storage Selection
| Requirement | Recommended Service |
|-------------|---------------------|
| General purpose, frequent access | S3 Standard |
| Unknown access patterns | S3 Intelligent-Tiering |
| Cost-optimized, infrequent access | S3 Standard-IA |
| Single-AZ resilience | S3 One Zone-IA |
| Archival, occasional access | S3 Glacier Instant Retrieval |
| Archival, rare access | S3 Glacier Flexible Retrieval |
| Long-term archival | S3 Glacier Deep Archive |

## Performance Optimization

### S3 Performance
- **Parallel requests**: Distribute workload across connections
- **Multi-part uploads**: Break large objects into parts for parallel upload
- **S3 Transfer Acceleration**: Use CloudFront for faster long-distance transfers
- **Request rate partitioning**: Design key names for optimal performance
- **S3 Select**: Retrieve only needed data to reduce transfer costs
- **Directory buckets**: Strongly consistent, high-performance workloads

### EBS Performance
- **IOPS vs. Throughput**: Choose volume type based on workload characteristics
- **Instance limits**: Ensure instance type supports desired EBS performance
- **Initialization**: Pre-warm volumes (not needed for new volumes created after 2016)
- **RAID configurations**: RAID 0 for higher performance, RAID 1 for increased durability
- **EC2 Nitro**: Higher EBS performance on Nitro-based instances

### EFS Performance
- **Performance mode selection**: General Purpose for low latency, Max I/O for high throughput
- **Throughput mode selection**: Based on workload predictability and variability
- **Transfer mode**: Consider enabling asynchronous writes (durability implications)
- **Connection distribution**: Distribute client connections across availability zones
- **File access patterns**: Optimize based on sequential vs. random access

## Cost Optimization Strategies

### S3 Cost Optimization
- **Storage class selection**: Match to access patterns
- **Lifecycle policies**: Automate transitions to cheaper storage classes
- **S3 Intelligent-Tiering**: For unknown or changing access patterns
- **Requester Pays**: For shared data where consumer should pay transfer costs
- **S3 Analytics**: Use to identify optimal lifecycle policies
- **S3 Inventory**: Analyze object metadata for cost optimization opportunities
- **S3 Select**: Reduce data transfer costs by filtering at source

### EBS Cost Optimization
- **Volume type selection**: Match to workload requirements
- **Snapshot management**: Delete unnecessary snapshots
- **Right-sizing**: Adjust volume size to actual needs
- **Elastic Volumes**: Modify volume type/size without downtime
- **EBS Snapshots Archive**: Lower-cost tier for infrequently accessed snapshots
- **Data Lifecycle Manager**: Automate snapshot management

### EFS Cost Optimization
- **Storage classes**: Use lifecycle management to move data to IA/Archive
- **Throughput mode**: Choose Bursting for variable workloads, Provisioned for predictable high throughput
- **EFS One Zone**: Single-AZ storage for non-critical data at lower cost
- **Access patterns**: Optimize mount options for workload characteristics

## Security Best Practices

### Encryption
- Enable encryption at rest for all storage services
- Use KMS customer managed keys for controlled access
- Consider client-side encryption for sensitive data
- Enable encryption in transit where available
- Regularly rotate encryption keys

### Access Control
- Use IAM policies with least privilege principle
- Implement bucket policies for S3 resource-based control
- Enable S3 Block Public Access settings
- Use VPC endpoints for private network access
- Implement MFA Delete for critical S3 buckets
- Use Access Points for application-specific permissions

### Monitoring and Audit
- Enable CloudTrail for all API activity
- Configure S3 Access Logs for object-level activity
- Use CloudWatch metrics for performance and usage
- Implement AWS Config rules for compliance
- Regular security assessments and reviews

## Resources for Further Learning
- [AWS Storage Services Overview](https://aws.amazon.com/products/storage/)
- [S3 Performance Guidelines](https://docs.aws.amazon.com/AmazonS3/latest/userguide/optimizing-performance.html)
- [EBS Volume Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)
- [EFS Performance](https://docs.aws.amazon.com/efs/latest/ug/performance.html)
- [Storage Best Practices Whitepaper](https://d1.awsstatic.com/whitepapers/AWS%20Storage%20Services%20Whitepaper-v9.pdf)

## Choosing the Right Storage Service

When selecting the appropriate AWS storage service, consider these key factors:

1. **Access Pattern**: How will you access the data? Random or sequential access?
2. **Performance Requirements**: What IOPS, throughput, and latency do you need?
3. **Sharing Requirements**: Will multiple instances/users need access?
4. **Durability and Availability**: What are your resilience requirements?
5. **Data Lifecycle**: How frequently will the data be accessed over time?
6. **Cost Considerations**: Which pricing model makes sense for your workload?

### Quick Decision Guide

**Choose EC2 Instance Store when you need:**
- Highest possible I/O performance
- Temporary storage for caches, buffers, or scratch data
- Storage included in instance cost
- Data replication handled at the application level

**Choose Amazon EBS when you need:**
- Persistent block storage with consistent performance
- Boot volumes for EC2 instances
- Database storage requiring low-latency
- Ability to detach and attach to other instances
- Point-in-time backups (snapshots)

**Choose Amazon S3 when you need:**
- Virtually unlimited scalable storage
- Web-accessible content
- Storage for static assets, backups, or archives
- Data lakes for analytics
- Regional or global data distribution

**Choose Amazon EFS/FSx when you need:**
- Shared file access across multiple instances
- Support for file system protocols (NFS, SMB)
- Elastic capacity with pay-as-you-go pricing
- Linux or Windows compatible file systems

---
*Return to [Main Study Guide](../aws_solutions_architect_study_guide.md)* 
