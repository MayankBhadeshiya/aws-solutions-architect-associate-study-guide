# AWS Hybrid and Container Solutions

## Overview
Hybrid architectures combine on-premises infrastructure with AWS cloud services, providing organizations with flexibility, cost benefits, and a gradual path to cloud migration. Container technologies enable consistent application deployment across environments. This guide covers key hybrid and container services, architectural patterns, and implementation examples for the AWS Solutions Architect Associate exam.

## Hybrid Fundamentals

### What is a Hybrid Architecture?
A hybrid architecture integrates on-premises infrastructure with cloud services, allowing organizations to:
- Keep sensitive workloads on-premises while leveraging cloud services
- Extend on-premises capacity with cloud resources during peak periods
- Gradually migrate to the cloud with a phased approach
- Maintain business continuity and disaster recovery capabilities
- Meet regulatory or data residency requirements

### Hybrid Architecture Components
- **Connectivity**: Secure, reliable network connections between on-premises and AWS
- **Identity Management**: Unified authentication and authorization across environments
- **Data Integration**: Seamless data flow between on-premises and cloud systems
- **Management and Monitoring**: Centralized operational oversight of hybrid resources
- **Security**: Consistent security controls across all environments

### Container Fundamentals
Containers provide a standard way to package application code, configurations, and dependencies into a single object that can run consistently across environments:
- **Isolation**: Each container runs as an isolated process
- **Lightweight**: Containers share the host OS kernel, requiring fewer resources than VMs
- **Portability**: Containers run consistently across different environments
- **Microservices**: Enable decomposition of applications into smaller, independent services
- **Scalability**: Containers can be rapidly deployed and scaled

## Hybrid Connectivity Options

### AWS Direct Connect
- **Dedicated Network Connection**: Provides a private, high-bandwidth connection between on-premises data centers and AWS
- **Connection Types**:
  - Dedicated Connections: 1 Gbps, 10 Gbps, and 100 Gbps ports
  - Hosted Connections: Various speeds available through AWS partners
- **Benefits**:
  - Consistent network performance
  - Reduced bandwidth costs
  - Increased security
  - Support for hybrid workloads
- **Virtual Interfaces (VIFs)**:
  - Private VIF: Connect to VPC resources
  - Public VIF: Connect to public AWS services
  - Transit VIF: Connect to Transit Gateway
- **Key Features**:
  - Shortest path to AWS resources
  - Isolation from public internet
  - Reduces chance of bottlenecks or latency issues
  - SiteLink for connecting offices and data centers across global network
  - Redundant AWS equipment when requesting multiple ports
- **Redundancy Options**:
  - Second Direct Connect connection for redundancy
  - Backup Site-to-Site VPN connection
  - If no backup is configured and Direct Connect fails:
    - VPC traffic will be dropped
    - Traffic to public resources will route over internet

### AWS Site-to-Site VPN
- **IPsec VPN Connection**: Encrypted connection between on-premises network and VPC
- **Components**:
  - Customer Gateway: Customer side of the VPN connection
  - Virtual Private Gateway: AWS side of the VPN connection (VPN concentrator)
- **Features**:
  - Two tunnels per connection for high availability
  - Support for static or dynamic routing (BGP)
  - Accelerated VPN option for improved performance
  - Can be configured as backup for Direct Connect
- **Use Cases**:
  - Quick connectivity solution
  - Backup for Direct Connect
  - Lower cost option for less critical workloads
- **Deployment Scenarios**:
  - Multiple customer gateway connections for redundancy
  - Multiple VPC connections

### AWS Transit Gateway
- **Network Transit Hub**: Connects VPCs and on-premises networks through a central hub
- **Key Features**:
  - Simplifies network architecture
  - Supports transitive routing
  - Regional resource with cross-region peering
  - Integrated with Direct Connect and VPN
  - Centralizes network management
  - Enables scalable network architecture
- **Attachments**:
  - VPC attachments
  - VPN attachments
  - Direct Connect Gateway attachments
  - Transit Gateway Connect
  - Peering attachments
- **Use Cases**:
  - Connecting multiple VPCs to on-premises networks
  - Simplifying complex network topologies
  - Creating hub-and-spoke network architectures
  - Connecting multiple AWS accounts

### AWS Client VPN
- **Managed Remote-Access VPN**: Enables remote users to securely access AWS and on-premises networks
- **Key Features**:
  - Fully elastic, scales automatically based on demand
  - Consistent user experience before, during, and after migrations
  - Supports OpenVPN protocol
- **Deployment Scenarios**:
  - Single VPC access
  - On-premises network access only
  - Client-to-client communication
  - Hybrid access (both VPC and on-premises)

### Connectivity Architecture Patterns
- **High Availability Hybrid Connection**:
  - Primary: Direct Connect
  - Backup: Site-to-Site VPN
  - Redundant Direct Connect at different locations
- **Multi-Region Hybrid Network**:
  - Transit Gateway in each region
  - Transit Gateway peering between regions
  - Direct Connect to multiple regions
- **Transit Hub and Spoke**:
  - Transit Gateway as central hub
  - VPCs and on-premises networks as spokes
  - Centralized security and routing controls
- **VPN and Direct Connect Failover**:
  - Direct Connect as primary connection
  - Site-to-Site VPN configured for automatic failover
  - Ensures continuous connection to AWS resources

## Container Services on AWS

### Amazon Elastic Container Service (ECS)
- **Fully managed container orchestration service**
- **Launch Types**:
  - Fargate (serverless): AWS manages the underlying infrastructure
  - EC2: You manage the EC2 instances 
    - Suitable when custom AMIs are required
    - Necessary when SSH access to underlying instances is needed
    - Provides more control over instance configuration
- **Key Components**:
  - Cluster: Logical grouping of tasks or services
  - Task Definition: Blueprint for application (container image, CPU, memory)
  - Task: Instance of a task definition running one or more containers
  - Service: Maintains a specified number of tasks
- **Integration**:
  - Load balancing with ALB/NLB
  - Service discovery
  - Auto scaling
  - IAM roles for tasks
- **Auto Scaling Options**:
  - Cluster Auto Scaling: Uses Auto Scaling groups with managed scaling
  - Service Auto Scaling:
    - Target tracking scaling (recommended): Based on target value for a specific metric
    - Step scaling: Based on scaling adjustments that vary with alarm breach size

### Amazon Elastic Kubernetes Service (EKS)
- **Managed Kubernetes service**
- **Deployment Options**:
  - Managed node groups
  - Self-managed nodes
  - Fargate (serverless)
- **Key Features**:
  - Kubernetes control plane across multiple AZs
  - Integration with AWS services
  - Support for EKS Distro (EKS-D)
  - EKS Anywhere for on-premises deployment
- **Use Cases**:
  - Organizations already using Kubernetes
  - Multi-cloud strategies
  - Complex orchestration requirements

### Amazon ECS Anywhere and EKS Anywhere
- **Extend container orchestration to on-premises environments**
- **ECS Anywhere**:
  - Run ECS tasks on customer-managed infrastructure
  - Consistent operational experience with cloud-based ECS
  - Uses AWS Systems Manager for agent management
  - Provides consistent tooling across environments
  - Reduces complexity of local container orchestration
  - Enables compliance while leveraging managed solutions
- **EKS Anywhere**:
  - Run Kubernetes clusters on-premises
  - Based on Amazon EKS Distro
  - Lifecycle management with EKS Console
- **Benefits**:
  - Consistent management experience
  - Reduced operational overhead
  - Simplified migration path

### AWS App2Container
- **Tool to containerize existing applications**
- **Supported Applications**:
  - Java applications on Linux or Windows
  - .NET applications on Windows
  - ASP.NET applications on Windows
- **Features**:
  - Automated containerization
  - Integration with ECS and EKS
  - CI/CD pipeline generation
- **Process**:
  - Analyze: Discover and analyze applications
  - Transform: Create container images and deployment artifacts
  - Deploy: Deploy containerized application to AWS

### Network Connectivity for Containers
- **NAT Gateways**:
  - Enable instances in private subnets to connect to the internet/external services
  - Prevent external services from initiating connections to those instances
  - Replace source IP address of instances with NAT device address
  - Available in two types:
    - Public NAT Gateway: Enables connection to internet through Internet Gateway
    - Private NAT Gateway: Enables connection to other VPCs or on-premises networks
  - Benefits over NAT instances:
    - Managed by AWS
    - High availability within AZ
    - Up to 45 Gbps bandwidth
    - No need to manage security groups
- **NAT Instances**:
  - Custom AMI providing network address translation
  - Launched in public subnet to allow outbound internet connectivity
  - Manually managed and scaled
  - Requires security group management
- **Connectivity Patterns for Containers**:
  - Private subnets with NAT Gateway for ECS/EKS clusters
  - Load balancers in public subnets for incoming traffic
  - Container-to-container communication within VPC
  - Service discovery for internal service communication

## Hybrid Storage Solutions

### AWS Storage Gateway
- **Hybrid storage service connecting on-premises environments to AWS storage**
- **Types of Storage Gateway**:
  - File Gateway: SMB and NFS interfaces to S3
  - Volume Gateway: iSCSI block storage with cloud backup
    - Cached Volumes: Primary data in S3, cache on-premises
    - Stored Volumes: Primary data on-premises, backup to S3
  - Tape Gateway: Virtual tape library using S3 and Glacier
- **Use Cases**:
  - File sharing and collaboration
  - Backup and archiving
  - Disaster recovery
  - Cloud bursting and migration

### Amazon S3 Outposts
- **S3 object storage on AWS Outposts rack**
- **Features**:
  - Single-Region access point
  - Local processing of data
  - Support for S3 APIs and features
- **Use Cases**:
  - Applications requiring local data processing
  - Data residency requirements
  - Low-latency access to local data

### Amazon EFS and FSx for On-Premises Access
- **Amazon EFS with AWS Direct Connect**: Access NFS file systems from on-premises
- **Amazon FSx for Windows File Server**: SMB access from on-premises
- **Amazon FSx for Lustre**: High-performance file system with Direct Connect access
- **Benefits**:
  - Consolidated file storage
  - Elimination of on-premises storage management
  - Consistent access across environments

## Hybrid Database Solutions

### Amazon RDS on Outposts
- **Fully managed RDS databases on AWS Outposts**
- **Supported Engines**:
  - MySQL
  - PostgreSQL
- **Features**:
  - Local database processing
  - Automatic backups to AWS Region
  - Same management experience as RDS in the cloud
- **Use Cases**:
  - Applications requiring local database processing
  - Data residency requirements
  - Consistent operations with RDS

### Amazon RDS High Availability Options
- **Multi-AZ Deployments**:
  - Synchronous replication to a standby instance in a different AZ
  - Automatic failover to standby instance during outages
  - No manual intervention required for failover
  - Available in two configurations:
    - One standby DB instance: Uses two Availability Zones
    - Two standby DB instances: Uses three Availability Zones for even greater availability
  - Failover typically completes in under 35 seconds
- **Read Replicas**:
  - Asynchronous replication to one or more read-only instances
  - Offload read traffic from primary instance
  - Can be promoted to standalone DB instances if needed
  - Available across multiple AWS regions
  - Use cases:
    - Scaling read operations
    - Reporting workloads
    - Disaster recovery preparation

### RDS Scaling Options
- **Vertical Scaling**:
  - Modify instance class to increase CPU and memory
  - Change between instance types for different workload requirements
- **Storage Scaling**:
  - Manual scaling: Allocate more storage
  - RDS Storage Auto Scaling: Automatically scales storage capacity in response to growing workloads
  - Set desired maximum storage limit
- **Performance Optimization**:
  - Storage type selection:
    - General Purpose SSD: Cost-effective for broad range of workloads
    - Provisioned IOPS: For I/O-intensive workloads requiring consistent performance
    - Magnetic: Legacy option, not recommended for new workloads

### AWS Database Migration Service (DMS)
- **Managed database migration service**
- **Capabilities**:
  - Homogeneous migrations (same database engines)
  - Heterogeneous migrations (different database engines)
  - Continuous data replication with minimal downtime
- **Components**:
  - Replication server in AWS Cloud
  - Source and target endpoints
  - Replication tasks
- **Process**:
  - AWS DMS creates target tables if they don't exist
  - Loads data from source to target
  - Captures ongoing changes for continuous replication
- **Use Cases**:
  - Database migrations to AWS
  - Database consolidation
  - Continuous data replication
  - Disaster recovery solutions

### Hybrid Database Architectures
- **Read Replicas in the Cloud**:
  - Primary database on-premises
  - Read replicas in AWS for read scaling and disaster recovery
- **Database Migration with Minimal Downtime**:
  - AWS DMS with ongoing replication
  - Switch over when ready to migrate fully
- **Distributed Data Architecture**:
  - Local databases for low-latency operations
  - Data synchronization to cloud databases
  - Global views with analytics in the cloud

## Hybrid Management and Operations

### AWS Systems Manager
- **Management service for hybrid and multi-cloud environments**
- **Key Capabilities**:
  - Inventory: Collect metadata about managed instances
  - State Manager: Define and maintain consistent configuration
  - Patch Manager: Automate patching process
  - Run Command: Execute commands across instances
  - Session Manager: Secure shell access without SSH/RDP ports
  - Parameter Store: Secure, hierarchical configuration storage
  - Automation: Simplify common maintenance tasks
- **Hybrid Activation**:
  - Register on-premises servers with SSM
  - Manage on-premises servers alongside AWS resources
- **Operations Management**:
  - Centralized visibility across hybrid environments
  - Automated operations procedures
  - Secure remote management
  - Consistent configuration management
  - Detailed operational insights through Explorer and OpsCenter

### AWS Storage Gateway
- **Hybrid storage service connecting on-premises environments to AWS storage**
- **Types of Storage Gateway**:
  - File Gateway: SMB and NFS interfaces to S3
    - Maintain NFS/SMB protocols for on-premises applications
    - Store data in S3 for durability and scalability
    - Local caching for frequently accessed data
  - Volume Gateway: iSCSI block storage with cloud backup
    - Cached Volumes: Primary data in S3, cache on-premises
    - Stored Volumes: Primary data on-premises, backup to S3
  - Tape Gateway: Virtual tape library using S3 and Glacier
- **Use Cases**:
  - File sharing and collaboration
  - Backup and archiving
  - Disaster recovery
  - Cloud bursting and migration

### AWS Backup
- **Centralized backup service**
- **Supported Resources**:
  - EC2, EBS, RDS, DynamoDB, EFS, Storage Gateway
  - On-premises VMware workloads
  - Applications supported by Windows VSS
  - FSx file systems (Windows File Server, Lustre, NetApp ONTAP, OpenZFS)
  - Neptune and DocumentDB databases
- **Features**:
  - Backup policies and scheduling
  - Cross-region backup
  - Integration with AWS Organizations
  - Centralized management console
  - Automated backup plans
- **Benefits**:
  - Consistent backup approach
  - Centralized management
  - Cost optimization
  - Simplified compliance
  - Reduced operational overhead

### AWS Cloud Map
- **Service discovery for cloud and on-premises resources**
- **Features**:
  - Custom namespaces for applications
  - Health checks for resources
  - DNS and API-based discovery
- **Benefits**:
  - Simplified service-to-service communication
  - Reduced configuration management
  - Support for hybrid architectures

## AWS Outposts, Wavelength, and Local Zones

### AWS Outposts
- **Fully managed AWS infrastructure deployed on-premises**
- **Form Factors**:
  - Outposts Rack: Starting at 42U, extendable to multiple racks
  - Outposts Servers: 1U and 2U servers for smaller needs
- **Supported Services**:
  - EC2, EBS, ECS, EKS, RDS, EMR
  - S3 on Outposts
  - Local Gateway for local network connectivity
- **Use Cases**:
  - Low-latency processing
  - Local data processing
  - Data residency requirements
  - Application migration

### AWS Wavelength
- **AWS infrastructure deployed within 5G networks**
- **Features**:
  - Ultra-low latency connections to 5G networks
  - Extension of VPC to Wavelength Zones
  - Support for EC2, EBS, and VPC features
- **Use Cases**:
  - IoT applications
  - Smart cities
  - AR/VR
  - Machine learning at the edge

### AWS Local Zones
- **AWS infrastructure deployed close to large population centers**
- **Features**:
  - Extension of a region
  - Low-latency access to select AWS services
  - Seamless connectivity to full range of services in the AWS Region
- **Use Cases**:
  - Media and entertainment workloads
  - Electronic design automation
  - Real-time gaming
  - ML inference

## Real-World Implementation Example: Insurance Company Migration

### Customer Requirements
- Migrate container-based applications to AWS
- Maintain some workloads on-premises due to regulatory requirements
- Ensure high-speed, secure connectivity between environments
- Centralize management and monitoring
- Minimize operational changes during migration

### Hybrid Container Solution Architecture
- **Connectivity Layer**:
  - AWS Direct Connect for primary connectivity
  - Site-to-Site VPN for backup
  - Transit Gateway for centralized routing
- **Compute Layer**:
  - EKS for container orchestration
  - EKS Anywhere for on-premises Kubernetes clusters
  - EC2 instances with Auto Scaling for supporting services
- **Storage Layer**:
  - S3 for object storage
  - EFS for shared file storage
  - Storage Gateway for hybrid storage integration
- **Database Layer**:
  - RDS for managed database services
  - RDS on Outposts for localized database requirements
  - DynamoDB for NoSQL needs
- **Management Layer**:
  - Systems Manager for unified management
  - CloudWatch for monitoring
  - AWS Backup for consistent backup strategy

### Implementation Approach
1. **Establish Hybrid Connectivity**:
   - Deploy Direct Connect with redundant connections
   - Set up Site-to-Site VPN as backup
   - Configure Transit Gateway for centralized routing
2. **Implement Container Platform**:
   - Deploy EKS clusters in AWS
   - Set up EKS Anywhere on-premises
   - Create consistent CIDR ranges and security groups
3. **Configure Shared Services**:
   - Deploy centralized logging and monitoring
   - Implement CI/CD pipeline for container deployments
   - Set up IAM roles and security controls
4. **Migrate Applications**:
   - Identify application dependencies
   - Refactor applications if needed
   - Migrate applications in phases based on priority
5. **Optimize Operations**:
   - Implement automated monitoring and alerting
   - Create runbooks for common operational tasks
   - Train teams on new tools and processes

### Migration Benefits
- **Improved Scalability**: Ability to scale applications based on demand
- **Enhanced Resilience**: Distributed architecture across on-premises and AWS
- **Operational Consistency**: Unified management experience
- **Cost Optimization**: Pay-as-you-go pricing for cloud resources
- **Innovation Acceleration**: Access to new AWS services and features

## Common Exam Scenarios

### Scenario: Hybrid Connectivity for an Enterprise
**Challenge**: Design a highly available hybrid connectivity solution for an enterprise with multiple on-premises data centers.
**Solution**:
- Direct Connect connections from each data center
- Site-to-Site VPN as backup
- Transit Gateway for centralized routing
- AWS RAM to share Transit Gateway across accounts
- Route tables configured for appropriate traffic flow

### Scenario: Containerized Application Migration
**Challenge**: Migrate containerized applications to AWS while maintaining some components on-premises.
**Solution**:
- EKS in AWS and EKS Anywhere on-premises
- Application Load Balancer for traffic distribution
- VPC endpoints for secure access to AWS services
- ECR for container image repository
- CI/CD pipeline with CodePipeline and CodeBuild

### Scenario: Hybrid Data Processing
**Challenge**: Implement a solution that processes data locally but leverages AWS for analytics and storage.
**Solution**:
- AWS Outposts for local processing
- S3 on Outposts for local storage
- DataSync for efficient data transfer
- Lake Formation and Athena for analytics
- QuickSight for visualization

### Scenario: Hybrid Disaster Recovery
**Challenge**: Design a disaster recovery solution for on-premises applications using AWS.
**Solution**:
- AWS Backup for consistent backups
- Storage Gateway for hybrid storage
- CloudEndure or AWS Application Migration Service
- Route 53 for DNS failover
- Periodic recovery testing with automated runbooks

## Best Practices for Hybrid and Container Architectures

### Connectivity and Networking
- Implement redundant connections between on-premises and AWS
- Use Transit Gateway for simplified network management
- Implement consistent security groups and NACLs
- Design for failure with automatic failover
- Implement centralized DNS management

### Container Management
- Use ECS for simpler container deployments
- Choose EKS for Kubernetes compatibility
- Implement proper resource limits and requests
- Use managed node groups for reduced operational overhead
- Implement cluster auto-scaling

### Security Considerations
- Implement consistent IAM policies across environments
- Use AWS Security Hub for centralized security management
- Implement encryption for data at rest and in transit
- Use private container registries with vulnerability scanning
- Implement network security controls at multiple layers

### Operations and Monitoring
- Use Systems Manager for unified management
- Implement centralized logging with CloudWatch Logs
- Create automated runbooks for common tasks
- Implement detailed monitoring with custom metrics
- Use X-Ray for distributed tracing

### Cost Optimization
- Right-size on-premises and cloud resources
- Implement container auto-scaling
- Use Spot Instances for non-critical workloads
- Implement data lifecycle policies
- Use Reserved Instances or Savings Plans for predictable workloads

## Hybrid Optimization Strategies

### Disaster Recovery Approaches
- **Backup and Restore**:
  - Low-cost approach for mitigating data loss
  - Back up data, configurations, and application code
  - Use infrastructure as code (IaC) for quicker redeployment
  - Suitable for non-critical workloads
- **Pilot Light**:
  - Core infrastructure always available but scaled down
  - Data continuously replicated from on-premises to AWS
  - Servers are configured but turned off until needed
  - Quick scaling of resources during DR events
- **Warm Standby**:
  - Scaled-down but fully functional copy of production environment
  - Always-on infrastructure in standby mode
  - Faster recovery than pilot light
  - Easier testing of DR procedures
- **Multi-Site Active/Active**:
  - Run workloads simultaneously in multiple locations
  - Real-time data replication
  - Immediate failover capabilities
  - Highest cost but near-zero recovery time
  - Provides geographic redundancy

### Storage Optimization
- **S3 Intelligent-Tiering**:
  - Automatically moves data between access tiers based on usage patterns
  - Optimizes storage costs without performance impact
  - Ideal for data with unknown or changing access patterns
  - No retrieval charges for accessed data
  - Access tiers include:
    - Frequent Access: For regularly accessed data
    - Infrequent Access: For data not accessed for 30+ days
    - Archive Instant Access: For rarely accessed data (90+ days)
    - Archive Access: For rarely accessed data with flexible retrieval times
    - Deep Archive Access: For data that rarely needs retrieval
  - Small monthly monitoring fee per object
- **RDS Storage Optimization**:
  - Enable storage autoscaling to automatically handle growth
  - Set maximum storage limits to control costs
  - Choose appropriate storage type based on workload
  - Monitor storage usage patterns
- **EBS Volume Optimization**:
  - Select appropriate volume types based on workload
  - Use gp3 volumes for better price-performance ratio
  - Implement automated snapshot lifecycle policies
  - Resize volumes based on actual usage patterns

### Network Optimization
- **Direct Connect with VPN Failover**:
  - Use Direct Connect for primary connectivity
  - Configure Site-to-Site VPN as automatic failover
  - Ensures continuous connectivity to AWS resources
  - Balances performance and cost
- **Transit Gateway Route Optimization**:
  - Centralize route management
  - Implement route prioritization
  - Use route tables to optimize traffic flows
  - Implement route propagation where appropriate
- **AWS Global Accelerator**:
  - Improve performance for global users
  - Optimize network path to applications
  - Route traffic over AWS global network
  - Static IP addresses that serve as fixed entry points

### Container Optimization
- **Right-Sizing Container Resources**:
  - Set appropriate CPU and memory limits
  - Analyze resource utilization patterns
  - Implement Fargate Spot for non-critical workloads
  - Use Compute Savings Plans for predictable usage
- **Auto Scaling Strategies**:
  - Implement cluster auto scaling to optimize underlying infrastructure
  - Configure service auto scaling with target tracking policies
  - Set appropriate scaling thresholds based on application patterns
  - Schedule scaling for predictable workload patterns

## Resources for Further Learning
- [AWS Hybrid Cloud Documentation](https://aws.amazon.com/hybrid/)
- [Amazon ECS Documentation](https://docs.aws.amazon.com/ecs/)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/)
- [AWS Outposts Documentation](https://docs.aws.amazon.com/outposts/)
- [AWS Direct Connect Documentation](https://docs.aws.amazon.com/directconnect/)
- [AWS Transit Gateway Documentation](https://docs.aws.amazon.com/vpc/latest/tgw/)
- [AWS Systems Manager Documentation](https://docs.aws.amazon.com/systems-manager/)
- [AWS Storage Gateway Documentation](https://docs.aws.amazon.com/storagegateway/)
- [AWS Database Migration Service Documentation](https://docs.aws.amazon.com/dms/)
- [Amazon RDS Documentation](https://docs.aws.amazon.com/rds/)
- [Disaster Recovery of Workloads on AWS Whitepaper](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-workloads-on-aws.html)
- [Coursera: Architecting Solutions on AWS](https://www.coursera.org/learn/architecting-solutions-on-aws)
- [AWS Containers Technical Guide](https://d1.awsstatic.com/whitepapers/aws-container-services-technical-guide.pdf)
- [AWS Network-to-Amazon VPC Connectivity Options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/network-to-amazon-vpc-connectivity-options.html)

---
*Return to [Main Study Guide](../aws_solutions_architect_pro_study_guide.md)* 