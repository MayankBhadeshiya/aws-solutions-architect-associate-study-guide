# AWS Compute Services

## Overview
AWS offers a diverse portfolio of compute services designed to meet various application requirements. Computing resources are the fundamental building blocks for running applications in the cloud. The right choice of compute service is crucial for building cost-effective, scalable, and highly available architectures.

### Understanding Compute Options
In AWS, compute services fall into three main categories:

1. **Virtual Machines (Amazon EC2)**: Traditional server-based computing that gives you full control over the operating system and installed software.

2. **Containers (Amazon ECS, Amazon EKS)**: Lightweight, portable computing environments that package code and dependencies together for consistent deployment across environments.

3. **Serverless (AWS Lambda, AWS Fargate)**: Run code without provisioning or managing servers, paying only for the compute time you consume.

When selecting a compute service, consider factors such as:
- Level of management required
- Operational responsibility
- Scaling needs
- Deployment frequency
- Cost model preferences
- Application architecture

## Amazon EC2 (Elastic Compute Cloud)

### What is Amazon EC2?
Amazon Elastic Compute Cloud (EC2) is a web service that provides resizable compute capacity in the cloud. It allows you to provision virtual servers (EC2 instances) with various configurations of CPU, memory, storage, and networking capacity.

### Amazon Machine Images (AMIs)
An Amazon Machine Image (AMI) provides the information required to launch an EC2 instance, including:

- **Operating System**: Linux, Windows, macOS
- **Application Software**: Pre-installed applications and services
- **Launch Permissions**: Who can use the AMI
- **Block Device Mappings**: Attached storage volumes

AMIs can be sourced from:
- **Quick Start AMIs**: Pre-made by AWS for common use cases
- **AWS Marketplace AMIs**: Commercial and open-source software from vendors
- **Community AMIs**: Contributed by the AWS community
- **Custom AMIs**: Created from your own EC2 instances

Creating custom AMIs enables you to standardize configuration, implement faster deployment, simplify scaling, and maintain version control of your server environments.

### EC2 Instance Lifecycle
EC2 instances transition through various states during their lifecycle:

1. **Pending**: Instance is being provisioned (no billing)
2. **Running**: Instance is operational and ready for use (billing starts)
3. **Stopping**: Instance is in the process of stopping
4. **Stopped**: Instance is shut down (no compute billing, but EBS volumes still incur charges)
5. **Shutting Down**: Instance is in the process of termination
6. **Terminated**: Instance has been permanently deleted (all billing stops)

Additional instance states include:
- **Rebooting**: Instance remains on same host with preserved IPs and data
- **Hibernating**: RAM contents saved to EBS for faster restart

### Instance Types Overview
| Family | Prefix | Optimized For | Use Cases |
|--------|--------|--------------|-----------|
| General Purpose | M, T | Balanced compute/memory/networking | Web servers, small databases, dev environments |
| Compute Optimized | C | High performance processors | Batch processing, gaming, scientific modeling |
| Memory Optimized | R, X, z | Memory-intensive applications | In-memory databases, real-time analytics |
| Storage Optimized | D, H, I | High disk throughput | Data warehousing, distributed file systems |
| Accelerated Computing | P, G, F, Inf, Trn | Hardware accelerators, co-processors | Machine learning, graphics processing |

### Instance Purchasing Options
| Option | Commitment | Discount | Availability | Use Case |
|--------|------------|----------|-------------|----------|
| On-Demand | None | None | Always available | Variable workloads |
| Reserved Instances | 1 or 3 years | Up to 72% | Capacity reservation | Predictable workloads |
| Savings Plans | 1 or 3 years | Up to 72% | Flexible usage | Mixed workloads |
| Spot Instances | None | Up to 90% | Can be terminated | Fault-tolerant workloads |
| Dedicated Hosts | On-demand or reserved | Varies | Physical isolation | Licensing, compliance |
| Capacity Blocks | Fixed duration | Cost-effective | Advanced reservation | ML/AI training jobs |

### EC2 Storage Options
| Storage Type | Description | Use Cases | Persistence |
|--------------|-------------|-----------|------------|
| EBS Volumes | Network-attached storage | Databases, boot volumes | Persists independently |
| Instance Store | Direct-attached storage | Temporary storage, caching | Terminated with instance |
| EFS | Elastic file system | Shared files, content management | Persists independently |
| S3 | Object storage | Static files, backups | Persists independently |

### Placement Groups
| Type | Description | Use Case | Limitations |
|------|-------------|----------|------------|
| Cluster | Single AZ, low latency | HPC, low-latency apps | Limited to one AZ, limited instance types |
| Spread | Instances on separate hardware | Critical applications | Max 7 instances per AZ per group |
| Partition | Groups of instances on separate racks | HDFS, HBase, Cassandra | Max 7 partitions per AZ |

### EC2 Features and Capabilities
- **AMI (Amazon Machine Image)**: Template for instance configuration
- **User Data**: Scripts that run at instance launch
- **Instance Metadata Service**: Access instance metadata from within the instance
- **Hibernation**: Save RAM contents to EBS for faster restart
- **Elastic IP**: Static public IP address that can be remapped
- **Enhanced Networking**: Higher bandwidth, lower latency
- **EC2 Nitro System**: Next-generation virtualization infrastructure

### EC2 Networking Concepts
- **Default VPC**: Automatically created for easy starting with EC2
- **Security Groups**: Stateful firewall for instances
- **Network ACLs**: Stateless firewall for subnets
- **Public/Private Subnets**: Control internet accessibility
- **Elastic Network Interfaces**: Virtual network cards

## Amazon EC2 Auto Scaling

### Auto Scaling Components
- **Launch Templates/Configurations**: Define what to launch
- **Auto Scaling Groups**: Define where and how many to launch
- **Scaling Policies**: Define when to launch or terminate

### Scaling Types
| Type | Description | Use Cases |
|------|-------------|-----------|
| Maintain | Keep specific number of instances running | Fault tolerance, availability |
| Manual | Manually change desired capacity | Testing, specialized workloads |
| Scheduled | Change capacity based on schedule | Predictable load changes |
| Dynamic | Respond to metrics and load | Variable traffic patterns |
| Predictive | Machine learning to predict capacity | Recurring patterns |

### Scaling Policies
- **Target Tracking**: Maintain a specific metric value (e.g., 70% CPU)
- **Step Scaling**: Add or remove instances based on alarm thresholds
- **Simple Scaling**: Basic add/remove capacity with cooldown
- **Predictive Scaling**: Proactive scaling based on forecast

### Advanced Auto Scaling Features
- **Instance Warmup**: Allow time for instances to initialize
- **Cooldown Periods**: Prevent thrashing
- **Health Checks**: Ensure instances are healthy
- **Lifecycle Hooks**: Custom actions during launch/terminate
- **Mixed Instance Types**: Combine instance types and purchase options
- **Capacity Rebalancing**: Proactively replace Spot Instances
- **Termination Policies**: Control which instances to terminate

## Container Services on AWS

### What are Containers?
Containers are standardized units that package code and all its dependencies together, ensuring the application runs quickly and reliably from one computing environment to another. Key advantages include:

- **Consistency**: Same environment from development to production
- **Lightweight**: Share OS kernel, more efficient than VMs
- **Portability**: Run anywhere the container runtime is supported
- **Isolation**: Applications run independently of each other
- **Microservice-friendly**: Ideal for distributed application architectures

### Docker and Containers
Docker is a popular container runtime that simplifies the management of the entire operating system stack needed for container isolation, including networking and storage. Docker makes it easy to create, package, deploy, and run containers.

### Comparison: Containers vs. Virtual Machines
| Feature | Containers | Virtual Machines |
|---------|------------|------------------|
| OS | Share host OS kernel | Contain full OS copy |
| Size | Megabytes | Gigabytes |
| Startup time | Seconds | Minutes |
| Isolation | Process-level | Complete |
| Resource efficiency | Higher | Lower |
| Portability | Very high | Limited |

## Amazon ECS (Elastic Container Service)

### Container Orchestration Service
- Fully managed container orchestration service
- Native AWS integration with services and IAM
- Supports Docker containers
- Multiple deployment options

### ECS Architecture Components
- **Clusters**: Logical grouping of tasks or services
- **Task Definitions**: Blueprint for applications (JSON format)
- **Tasks**: Instantiation of a task definition
- **Services**: Long-running tasks with scaling and load balancing
- **Container Instances**: EC2 instances running ECS agent (EC2 launch type)

### Launch Types
| Launch Type | Description | Use Cases | Management |
|-------------|-------------|-----------|------------|
| EC2 | Run on EC2 instances you manage | More control, reserved instances, specific instance types | Customer managed instances |
| Fargate | Serverless compute for containers | Simplified operations, variable workloads | AWS managed compute |

### Networking Modes
- **awsvpc**: Each task gets its own ENI and security group
- **bridge**: Docker's default networking using bridges
- **host**: Bypass Docker networking, use host's network directly
- **none**: Disable networking

### Service Discovery
- **AWS Cloud Map**: Service discovery for ECS
- **Route 53 Auto Naming**: Register service instance endpoints
- **DNS-based discovery**: Automatic DNS record management

### ECS Deployment Strategies
- **Rolling Update**: Gradually replace tasks
- **Blue/Green with CodeDeploy**: Zero-downtime deployments
- **External**: Custom deployment logic

### ECS Task Placement Strategies
- **binpack**: Fill instances based on memory or CPU
- **random**: Place tasks randomly
- **spread**: Distribute tasks evenly across instances/AZs
- **distinctInstance**: Place each task on a different container instance

## Amazon EKS (Elastic Kubernetes Service)

### Managed Kubernetes Service
- AWS-managed Kubernetes control plane
- Certified Kubernetes conformant
- Integrates with AWS services
- Multi-AZ control plane for high availability

### EKS Architecture
- **Control Plane**: Managed by AWS across three AZs
- **Worker Nodes**: Self-managed, managed node groups, or Fargate
- **Add-ons**: Operational software for Kubernetes functionality

### Deployment Options
| Option | Description | Use Cases |
|--------|-------------|-----------|
| Self-managed nodes | You manage the EC2 instances | Custom AMIs, specific instance types |
| Managed node groups | EKS provisions and manages nodes | Simplified operations |
| Fargate | Serverless Kubernetes pods | Pod-level isolation, variable workloads |

### Networking in EKS
- **Amazon VPC CNI**: Native VPC networking
- **Custom CNIs**: Calico, Weave Net, etc.
- **Service networking**: ClusterIP, NodePort, LoadBalancer
- **Ingress controllers**: ALB Ingress Controller, NGINX

### EKS Security Features
- **IAM integration**: Pod IAM roles with IRSA
- **Security groups for pods**: VPC security groups for individual pods
- **Pod security policies**: Control security-sensitive aspects of pod specification
- **Network policies**: Control traffic flow between pods

### EKS Storage Options
- **EBS CSI Driver**: Dynamic provisioning of EBS volumes
- **EFS CSI Driver**: Shared file system across pods
- **FSx for Lustre CSI Driver**: High-performance file system
- **S3 integration**: Object storage access

## Serverless Computing on AWS

### What is Serverless?
Serverless computing allows you to build and run applications without thinking about servers. It provides four key benefits:

1. **No server management**: Focus on code, not infrastructure
2. **Automatic scaling**: Handles any workload size automatically
3. **Pay-per-use**: Only pay for what you use, never for idle
4. **Built-in availability and fault tolerance**: No extra work required

### Serverless Compute Options on AWS
AWS offers multiple serverless compute options:
- **AWS Lambda**: Run code without provisioning servers
- **AWS Fargate**: Run containers without managing infrastructure
- **Amazon Aurora Serverless**: On-demand, auto-scaling database
- **Amazon DynamoDB**: Serverless NoSQL database

## AWS Lambda

### Serverless Compute Service
- Run code without provisioning servers
- Supports Node.js, Python, Java, Go, .NET, Ruby
- 15-minute maximum execution time
- Up to 10GB memory allocation (CPU scales with memory)
- 512MB /tmp storage (ephemeral)
- 1000 concurrent executions default (can be increased)

### Lambda Components
- **Function**: Your code and configuration
- **Trigger**: Event source that invokes the function
- **Runtime**: Language-specific environment to execute code
- **Handler**: Method in your code that processes events

### Lambda Function Handler
- Entry point for Lambda invocations
- Processes events and executes code
- Follows language-specific patterns (e.g., `def handler_name(event, context)` for Python)

### Lambda Integration Points
| Trigger Type | Examples | Use Cases |
|--------------|----------|-----------|
| AWS Services | S3, DynamoDB, SQS, EventBridge | Event-driven processing |
| HTTP Endpoints | API Gateway, ALB | Web applications, microservices |
| Function Orchestration | Step Functions | Complex workflows |
| Developer Tools | CodePipeline, CodeCommit | CI/CD automation |

### Deployment Options
- **Functions**: Single-function deployment
- **Layers**: Shared code and dependencies
- **Container Images**: Package as container up to 10GB
- **Infrastructure as Code**: CloudFormation, SAM, CDK

### Advanced Lambda Features
- **Provisioned Concurrency**: Pre-initialized instances for consistent performance
- **Versions and Aliases**: Manage deployment versions
- **Destination**: Configure success/failure destinations
- **Function URLs**: Direct HTTPS endpoints
- **Async Invocation**: Retry behavior and DLQ configuration
- **VPC Access**: Lambda in VPC with ENIs
- **Lambda Extensions**: Monitoring, security, governance tools

### Lambda Billing
- Pay only for what you use:
  - Number of requests
  - Duration (execution time in milliseconds)
  - Memory allocated
- No charge when code is not running
- Millisecond billing granularity

### Best Practices for Lambda
- Minimize cold starts with provisioned concurrency
- Optimize memory allocation for performance
- Reuse execution context with global variables
- Use environment variables for configuration
- Implement monitoring with CloudWatch

## AWS Fargate

### Serverless Compute for Containers
- Run containers without managing servers
- Pay only for resources used by containers
- Works with both ECS and EKS
- Isolated compute environment per task/pod

### Fargate Task Components
- **vCPU and memory**: Fine-grained resource allocation
- **Networking**: awsvpc mode only (dedicated ENI)
- **Storage**: Ephemeral storage with optional EFS integration

### Fargate Platform Versions
- Controls runtime environment and features
- Regular updates for security and functionality
- Different version streams for ECS and EKS

### Fargate Spot (ECS only)
- Use Spot capacity for fault-tolerant workloads
- Up to 70% cost savings
- Requires handling of SIGTERM for graceful shutdowns

## AWS Batch

### Fully Managed Batch Processing
- Run batch computing workloads
- Dynamically provisions optimal compute resources
- No need to install or manage batch software
- Supports containers and traditional applications

### Batch Components
- **Jobs**: Unit of work submitted to AWS Batch
- **Job Definitions**: Specification for jobs (similar to task definitions)
- **Job Queues**: Jobs are submitted to queues where they wait to be scheduled
- **Compute Environments**: Resources that jobs run on

### Compute Environment Types
- **Managed**: AWS manages capacity and instance types
- **Unmanaged**: You manage your own compute resources

### Batch Scheduling
- **FIFO**: First in, first out
- **Priority based**: Higher priority jobs run first
- **Fair share scheduling**: Resource allocation across users/jobs

### Integration with Other Services
- **ECS**: Uses ECS to run containerized batch jobs
- **EC2**: Uses EC2 instances for traditional batch jobs
- **Spot Instances**: Cost-effective for batch workloads
- **Lambda**: Trigger batch jobs from Lambda functions

## Lightsail

### Simplified Virtual Private Servers
- Easy-to-use cloud platform for simple workloads
- Fixed pricing with bundled resources
- Preconfigured applications and development stacks
- Suitable for small websites, dev/test environments

### Lightsail Components
- **Instances**: Virtual private servers with fixed resources
- **Managed Databases**: MySQL and PostgreSQL
- **Load Balancers**: Distribute traffic to instances
- **Block Storage**: Additional storage for instances
- **CDN Distributions**: Content delivery network

## Amazon AppRunner

### Fully Managed Container Service
- Simplified container and source code deployments
- Automatic scaling, load balancing, and HTTPS
- CI/CD pipeline integration
- VPC connectivity options

## Common Exam Scenarios

### Lift and Shift Migration
**Scenario**: Migrate on-premises applications to AWS with minimal changes.
**Solution**:
- EC2 instances with similar specifications to on-premises
- Auto Scaling for high availability and elasticity
- Application Load Balancer for traffic distribution
- Consider EC2 Dedicated Hosts for licensing requirements
- Options for persistent storage (EBS, EFS)

### Containerization Strategy
**Scenario**: Design a containerization strategy for a mix of applications.
**Solution**:
- ECS with Fargate for microservices and variable workloads
- ECS with EC2 for predictable workloads and custom requirements
- EKS for Kubernetes-native applications and multi-cloud strategy
- ECR for container image storage
- ALB for load balancing with path-based routing

### Serverless Application Architecture
**Scenario**: Design a cost-effective, scalable architecture for a new application.
**Solution**:
- Lambda for request processing and event handling
- API Gateway for RESTful API frontend
- DynamoDB for data storage
- S3 for static assets
- CloudFront for content delivery
- Step Functions for complex workflows

### High Performance Computing
**Scenario**: Design a solution for scientific computing workloads.
**Solution**:
- EC2 with specialized instance types (C, P, G families)
- Placement Groups (Cluster) for low-latency networking
- Spot Instances for cost optimization
- AWS Batch for job scheduling
- FSx for Lustre for high-performance storage
- Consider Capacity Blocks for ML workloads

### Global Application Deployment
**Scenario**: Deploy an application globally with low latency.
**Solution**:
- Multi-region EC2 deployments
- Auto Scaling in each region
- Route 53 with latency-based routing
- CloudFront for static content
- Global Accelerator for dynamic content
- Consider containerization for consistent deployments

## Compute Service Selection Guide

### Decision Framework
- **Control vs. Convenience**: Full control (EC2) to fully managed (Lambda)
- **Provisioning time**: Minutes (EC2) to milliseconds (Lambda, Fargate)
- **Scaling characteristics**: Manual, scheduled, or automatic
- **Cost model**: Always-on vs. pay-per-use
- **Operational model**: Infrastructure management vs. application focus

### Comparison Table
| Compute Service | Management Level | Scaling | Cost Model | Use Cases |
|-----------------|-----------------|---------|------------|-----------|
| EC2 | Customer managed | Manual/Auto Scaling | Per instance-hour | Traditional applications, full control |
| Lambda | Fully managed | Automatic | Pay per invocation/duration | Event processing, microservices |
| ECS + EC2 | Partially managed | Manual/Auto Scaling | Per EC2 instance | Containerized apps, cost control |
| ECS + Fargate | Fully managed | Automatic | Pay per task resources | Containerized apps, less management |
| EKS + EC2 | Partially managed | Manual/Auto Scaling | Per EC2 instance | Kubernetes workloads, control |
| EKS + Fargate | Mostly managed | Automatic | Pay per pod resources | Kubernetes workloads, less management |
| Batch | Fully managed | Automatic | Pay for compute used | Batch processing jobs |
| Lightsail | Fully managed | Limited | Fixed monthly | Simple websites, dev environments |
| AppRunner | Fully managed | Automatic | Pay per compute used | Web applications, APIs |

## Operational Excellence

### Cost Optimization
- Right-sizing instances for workloads
- Using appropriate purchase options (Reserved, Spot, Savings Plans)
- Implementing auto-scaling to match demand
- Using serverless for variable workloads
- Containerization for higher resource utilization

### Performance Efficiency
- Instance type selection based on workload characteristics
- Enhanced networking for throughput-intensive applications
- Placement groups for low-latency requirements
- EC2 Nitro system for improved performance
- Graviton processors for price-performance benefits

### Reliability
- Multi-AZ deployments for high availability
- Auto Scaling for fault tolerance
- Load balancing for traffic distribution
- Health checks and automatic recovery
- Disaster recovery strategies (backup/restore, pilot light, warm standby)

### Security
- IAM roles for secure application access
- Security groups and NACLs for network security
- Encryption at rest and in transit
- VPC isolation and private subnets
- AWS Systems Manager for secure operations

## Best Practices

### EC2 Best Practices
- Use the latest generation instances for better price-performance
- Implement proper tagging for resource management
- Use instance metadata service for dynamic configuration
- Leverage EC2 Auto Scaling for availability and elasticity
- Configure appropriate monitoring and alerting

### Container Best Practices
- Implement infrastructure as code for container deployments
- Use ECR image scanning for security
- Implement proper secrets management
- Design for statelessness and immutability
- Consider using managed services (Fargate) to reduce operational overhead

### Serverless Best Practices
- Optimize Lambda functions for performance
- Use appropriate memory settings
- Implement proper error handling and DLQ
- Leverage Lambda layers for shared code
- Design for concurrency and throttling limits

## Resources for Further Learning
- [AWS Compute Services](https://aws.amazon.com/products/compute/)
- [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/)
- [Amazon ECS Documentation](https://docs.aws.amazon.com/ecs/)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/)
- [AWS Serverless](https://aws.amazon.com/serverless/)
- [AWS Containers](https://aws.amazon.com/containers/)
- [AWS Compute Blog](https://aws.amazon.com/blogs/compute/)

---
*Return to [Main Study Guide](../aws_solutions_architect_study_guide.md)* 
