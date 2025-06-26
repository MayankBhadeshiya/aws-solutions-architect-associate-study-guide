# AWS Networking and Content Delivery

## Overview
AWS offers a comprehensive suite of networking services that provide the building blocks to design both simple and complex networks. Understanding these services is critical for the AWS Solutions Architect Associate exam, as networking forms the foundation of secure, scalable, and high-performance architectures.

## Networking Fundamentals

### What is Networking?
Networking is how computers around the world connect and communicate with each other. Just as AWS has created a network of resources using data centers, Availability Zones, and Regions, you'll need to understand how to connect resources within AWS and to external networks.

### IP Addressing
For computers to communicate, they need addresses to identify each other:

- **IP Address**: A unique identifier assigned to each device on a network
- **IPv4**: 32-bit addresses expressed in four octets (e.g., 192.168.1.30)
- **IPv6**: 128-bit addresses that provide vastly more address space than IPv4
- **Public IP**: Accessible over the internet
- **Private IP**: Only accessible within a private network

### CIDR Notation
Classless Inter-Domain Routing (CIDR) notation is a compressed way to specify a range of IP addresses:

- Written as an IP address followed by a forward slash and a number (e.g., 192.168.1.0/24)
- The number after the slash specifies how many bits of the IP address are fixed
- The remaining bits can be either 0 or 1, determining the number of IP addresses in the range
- Formula: 2^(32-prefix) = number of IP addresses
  - /24 provides 2^8 = 256 addresses
  - /16 provides 2^16 = 65,536 addresses
- In AWS, VPC CIDR blocks can range from /16 (large) to /28 (small)

### Binary Representation
IPv4 addresses are 32-bit binary numbers, typically presented in decimal format for readability:

- A 32-bit binary address: 11000000.10101000.00000001.00011110
- Converted to decimal: 192.168.1.30
- Each group of 8 bits (octet) is converted to a decimal number (0-255)

## Amazon Virtual Private Cloud (VPC)

### What is Amazon VPC?
Amazon VPC is a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define. You have complete control over your virtual networking environment, including:

- Selection of IP address range
- Creation of subnets
- Configuration of route tables and network gateways
- Implementation of security settings

### VPC Creation
When creating a VPC, you need to specify:
1. **Name**: A descriptive identifier for your VPC
2. **Region**: The AWS Region where the VPC will reside
3. **CIDR Block**: The IP address range for your VPC (e.g., 10.0.0.0/16)

### Subnets
Subnets are segments of a VPC's IP address range where you can place groups of isolated resources. When creating a subnet, you specify:

1. **VPC**: The parent VPC for this subnet
2. **Availability Zone**: The AZ where the subnet will reside
3. **CIDR Block**: A subset of the VPC CIDR block (e.g., 10.0.1.0/24)

#### Subnet Types
- **Public Subnet**: Has a route to an Internet Gateway, resources can receive public IP addresses
- **Private Subnet**: No direct route to an Internet Gateway, typically uses NAT Gateway for outbound internet access
- **Isolated Subnet**: No internet connectivity (neither inbound nor outbound)

#### High Availability
For fault tolerance, always deploy resources across at least two Availability Zones:
- Create subnets in multiple AZs
- Distribute resources across these subnets
- Ensure proper route tables and connectivity for each subnet

#### Reserved IP Addresses
AWS reserves the first four and the last IP address in each subnet CIDR block:
- **First address**: Network address
- **Second address**: VPC router
- **Third address**: Reserved for DNS
- **Fourth address**: Reserved for future use
- **Last address**: Network broadcast address

Example: In a subnet with CIDR 10.0.0.0/24 (256 addresses), only 251 IP addresses are available for use.

### Core Components
| Component | Description | Limits | Scope |
|-----------|-------------|--------|-------|
| VPC | Logically isolated network | 5 per region (default) | Region |
| Subnet | Range of IP addresses within a VPC | 200 per VPC | Availability Zone |
| Route Table | Rules to direct network traffic | 200 per VPC | VPC |
| Internet Gateway | Connect VPC to the internet | 1 per VPC | VPC |
| NAT Gateway | Allow private subnet internet access | 1 per AZ (HA) | Availability Zone |
| Security Group | Stateful firewall at instance level | 60 per ENI | VPC |
| Network ACL | Stateless firewall at subnet level | 1 per subnet | Subnet |
| VPC Endpoints | Private connection to AWS services | Gateway/Interface | VPC |

### Gateways

#### Internet Gateway (IGW)
- Acts like a modem connecting your VPC to the internet
- Highly available and horizontally scalable by default
- Enables resources in public subnets to access the internet
- Performs network address translation (NAT) for instances with public IPs

#### Virtual Private Gateway (VGW)
- Connects your AWS VPC to another private network
- Serves as the AWS side endpoint for VPN connections
- Works with a customer gateway on your side
- Enables encrypted VPN connections between AWS and on-premises networks

### VPC Routing

#### Route Tables
A route table contains a set of rules (routes) that determine where network traffic is directed:

- **Main Route Table**: Created automatically with your VPC
- **Custom Route Tables**: Created for specific routing needs
- **Local Route**: Enables communication within the VPC
- **Routes to Gateways**: Direct traffic to Internet Gateway, VPN, etc.

**Example Main Route Table**:
| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |

**Example Public Subnet Route Table**:
| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | igw-id |

**Example Private Subnet Route Table**:
| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | nat-gateway-id |

### VPC Security

#### Network Access Control Lists (NACLs)
- Act as a firewall at the subnet level
- Stateless: return traffic must be explicitly allowed
- Processed in order (lowest number first)
- Can allow or deny traffic
- Default NACL allows all inbound and outbound traffic

**Example NACL**:
| Rule # | Type | Protocol | Port Range | Source | Allow/Deny |
|--------|------|----------|------------|--------|------------|
| 100 | HTTP | TCP | 80 | 0.0.0.0/0 | ALLOW |
| 110 | HTTPS | TCP | 443 | 0.0.0.0/0 | ALLOW |
| 120 | SSH | TCP | 22 | 192.0.2.0/24 | ALLOW |
| * | All Traffic | All | All | 0.0.0.0/0 | DENY |

#### Security Groups
- Act as a firewall at the instance/interface level
- Stateful: return traffic automatically allowed
- Can only allow traffic (cannot deny)
- Default security group allows all outbound traffic but no inbound traffic
- All rules are evaluated before deciding to allow traffic

**Example Security Group**:
| Type | Protocol | Port Range | Source |
|------|----------|------------|--------|
| HTTP | TCP | 80 | 0.0.0.0/0 |
| HTTPS | TCP | 443 | 0.0.0.0/0 |
| SSH | TCP | 22 | 192.0.2.0/24 |

#### Security Group vs NACL Comparison
| Feature | Security Group | NACL |
|---------|---------------|------|
| Scope | Instance level | Subnet level |
| State | Stateful | Stateless |
| Rule types | Allow only | Allow and Deny |
| Rule evaluation | All rules | In order by rule number |
| Default rules | Allow outbound, deny inbound | Allow all inbound and outbound |

#### Multi-Tier Security
A common design pattern is to organize resources into different security tiers:
- **Web Tier**: Public-facing with limited internet access (HTTP/HTTPS)
- **Application Tier**: Private subnet accessing specific services
- **Database Tier**: Highly restricted with specific port access

This creates defense-in-depth with multiple security layers protecting your most valuable assets.

### Troubleshooting Network Connectivity
When troubleshooting EC2 instance connectivity issues, check the following:

1. **Internet Gateway**: Ensure an IGW is attached to your VPC
2. **Route Tables**: Confirm appropriate routes exist (0.0.0.0/0 → IGW for public subnets)
3. **Security Groups**: Verify inbound/outbound rules allow necessary traffic
4. **NACLs**: Check for overly restrictive subnet-level filtering
5. **Public IP**: Confirm the instance has a public IP address
6. **Protocol**: Verify you're accessing the correct protocol (HTTP vs HTTPS)
7. **Instance State**: Ensure the instance is running properly
8. **User Data**: Check if bootstrap scripts ran successfully
9. **IAM Permissions**: Verify appropriate IAM roles are attached
10. **Network Restrictions**: Check if your client network is blocking access

## Advanced VPC Design

#### Subnet Types and Patterns
- **Public Subnets**: Direct route to IGW, public IP addresses
- **Private Subnets**: No direct internet access, NAT for outbound
- **Isolated Subnets**: No internet access at all
- **Transit Subnets**: Used for transit gateway attachments
- **Database Subnets**: Dedicated to database instances

#### CIDR Planning Strategies
```
Enterprise Network: 10.0.0.0/8
├── Region 1: 10.0.0.0/12
│   ├── VPC 1: 10.0.0.0/16
│   │   ├── AZ-1a: 10.0.0.0/18
│   │   │   ├── Public: 10.0.0.0/24
│   │   │   ├── Private: 10.0.1.0/24
│   │   │   └── DB: 10.0.2.0/24
│   │   ├── AZ-1b: 10.0.64.0/18
│   │   │   ├── Public: 10.0.64.0/24
│   │   │   ├── Private: 10.0.65.0/24
│   │   │   └── DB: 10.0.66.0/24
│   ├── VPC 2: 10.1.0.0/16
...
```

### VPC Endpoints
- **Gateway Endpoints**: S3 and DynamoDB only
- **Interface Endpoints (AWS PrivateLink)**: For most AWS services
- **Benefits**: Reduced data transfer costs, improved security, reduced latency

### VPC Peering
- Direct connection between two VPCs
- Non-transitive (A→B and B→C does not mean A→C)
- Does not support overlapping CIDR blocks
- Works across regions and accounts

### Transit Gateway
- Hub-and-spoke connectivity between VPCs and on-premises
- Transitive routing (A→TGW→B and B→TGW→C means A→TGW→C)
- Route tables control traffic flow
- Supports multicast
- Integrated with Direct Connect and VPN

## Hybrid Networking

### AWS Direct Connect
- Dedicated network connection to AWS
- Available bandwidths: 1Gbps, 10Gbps, 100Gbps
- Connection types:
  - **Dedicated**: Direct physical connection
  - **Hosted**: Through AWS partners

#### Virtual Interfaces (VIFs)
| VIF Type | Purpose | Features |
|----------|---------|----------|
| Private VIF | Connect to VPC | Direct access to private IP addresses |
| Public VIF | Connect to public AWS services | Access to public AWS endpoints |
| Transit VIF | Connect to Transit Gateway | Multiple VPC connectivity |

#### Direct Connect Gateway
- Connect multiple VPCs across regions
- Single connection to multiple VPCs
- No transitive routing between VPCs

#### Resilient Connectivity Designs
- **Maximum Resiliency**: Multiple connections to multiple locations
- **High Resiliency**: Multiple connections to one location
- **Development/Test**: Single connection

### AWS Site-to-Site VPN
- IPsec VPN connection over the internet
- Supports static routes or dynamic routing with BGP
- Terminated on AWS Virtual Private Gateway or Transit Gateway
- Two tunnels per connection for redundancy

### AWS Client VPN
- OpenVPN-based managed client VPN service
- Connect from anywhere to AWS or on-premises
- Authentication options: Active Directory, certificate-based, SAML
- Endpoint runs in a VPC and specific subnets

## Advanced Connectivity Patterns

### Hybrid DNS
- Route 53 Resolver Endpoints
  - **Inbound**: Resolve AWS DNS from on-premises
  - **Outbound**: Resolve on-premises DNS from AWS
- Conditional forwarding rules

### Multi-Region Connectivity
- Inter-region Transit Gateway peering
- Inter-region VPC peering
- Global Accelerator for IP address preservation

### Transit Gateway Connect
- GRE-based attachment for SD-WAN integration
- Higher bandwidth than IPsec
- Simplified connectivity for compatible devices

## Route 53 DNS Service

### Routing Policies
| Policy Type | Use Case | Features |
|-------------|----------|----------|
| Simple | Single destination | No health checks |
| Weighted | A/B testing, gradual migration | Percentage-based routing |
| Latency | Multi-region applications | Routes based on lowest latency |
| Failover | Active/passive setups | Automatic failover based on health checks |
| Geolocation | Regional compliance, content localization | Routes based on user location |
| Geoproximity | Traffic distribution based on distance | Bias adjustments to control traffic flow |
| Multi-value | Simple load balancing | Returns multiple healthy records |

### Health Checks
- HTTP, HTTPS, TCP health checks
- CloudWatch alarm integration
- Calculated health checks (combine multiple checks)

### Route 53 Resolver
- DNS resolution within VPCs
- Integration with on-premises DNS
- Rules-based conditional forwarding

### Private Hosted Zones
- DNS resolution within VPCs
- Can be associated with multiple VPCs
- Split-horizon DNS with public hosted zones

## Elastic Load Balancing

### Load Balancer Types
| Type | Layer | Use Cases | Features |
|------|-------|-----------|----------|
| Application Load Balancer (ALB) | Layer 7 | HTTP/HTTPS applications | Content-based routing, WAF integration |
| Network Load Balancer (NLB) | Layer 4 | TCP/UDP, high performance | Ultra-low latency, static IP, preserve source IP |
| Gateway Load Balancer (GWLB) | Layer 3/4 | Network appliances | Transparent inspection, scales with traffic |
| Classic Load Balancer (CLB) | Layer 4/7 | Legacy applications | Basic load balancing (not recommended for new deployments) |

### ALB Features
- Path-based routing
- Host-based routing
- HTTP header-based routing
- Method-based routing
- Source IP-based routing
- Query string parameter-based routing
- Authentication integration (Cognito, OIDC)
- Sticky sessions
- WAF integration for security

### NLB Features
- Ultra-low latency
- Millions of requests per second
- Static IP addresses
- Preserve client source IP
- Support for TCP, UDP, TLS
- PrivateLink support
- Zonal isolation

### GWLB Features
- Transparent network inspection
- Centralized deployment of network appliances
- Scales with traffic demand
- Preserves IP addressing
- Used for firewalls, IDS/IPS, deep packet inspection

### Advanced Load Balancer Patterns
- Cross-zone load balancing
- Security group configuration
- Private load balancers
- Hybrid connectivity with on-premises
- TLS termination and re-encryption

## Amazon CloudFront

### Global Content Delivery Network
- 310+ Points of Presence globally
- DDoS protection with AWS Shield
- Integration with AWS WAF
- Origin failover capabilities

### Origins
- **S3 Origins**: Static content delivery, OAC for security
- **Custom Origins**: EC2, ALB, on-premises servers
- **Origin Groups**: Automatic failover between origins

### Cache Behaviors
- Path pattern matching
- Cache key management
- TTL settings
- Query string forwarding
- Cookie forwarding
- HTTP header forwarding
- HTTP method handling

### Security Features
- HTTPS enforcement
- Field-level encryption
- Origin access control (OAC)
- Signed URLs and cookies
- Geo-restriction
- AWS WAF integration

### Lambda@Edge and CloudFront Functions
| Feature | Lambda@Edge | CloudFront Functions |
|---------|-------------|----------------------|
| Language | Node.js, Python | JavaScript |
| Max. Execution Time | 5-30 seconds | 1ms |
| Memory | 128MB-10GB | 2MB |
| Use Cases | Complex transformations | Simple transformations |
| Triggers | Viewer/Origin Request/Response | Viewer Request/Response |

### Optimizations
- Origin Shield for reduced origin load
- Compression
- Real-time logs
- Cache invalidation strategies
- Origin failover for high availability

## AWS Global Accelerator

### Overview
- Anycast IP addresses (2 static IPs)
- Global network to reduce latency
- Automatic failover across regions
- Health checking capabilities

### Components
- Static anycast IP addresses
- Accelerator (standard or custom routing)
- Endpoint groups (regional)
- Endpoints (ALB, NLB, EC2, EIP)

### Use Cases
- Multi-region applications
- Gaming applications (UDP)
- IoT applications
- Voice/video applications
- Static IP preservation
- Fast regional failover

### Comparison with CloudFront
| Feature | Global Accelerator | CloudFront |
|---------|-------------------|------------|
| Type | Network layer (IP) | Content delivery (HTTP) |
| Cache | No caching | Content caching |
| Protocol | TCP, UDP | HTTP/S |
| Optimization for | Static IP, regional failover | Content delivery, dynamic content |
| Security | DDoS protection | DDoS, WAF, field-level encryption |

## Common Exam Scenarios

### Hybrid Connectivity Design
**Scenario**: Enterprise with multiple global offices needing secure, high-performance access to AWS resources.
**Solution**:
- Direct Connect with redundant connections for critical locations
- Site-to-Site VPN as backup or for smaller locations
- Transit Gateway for hub-and-spoke connectivity
- Route 53 Resolver for hybrid DNS resolution
- Private Link for SaaS integration

### Global Application Deployment
**Scenario**: Global web application requiring low latency and regional compliance.
**Solution**:
- Multi-region architecture with regional VPCs
- CloudFront for content delivery with appropriate cache behaviors
- Route 53 with geolocation or latency-based routing
- Global Accelerator for dynamic content
- DynamoDB Global Tables or Aurora Global Database
- Regional certificate management

### Security and Compliance Requirements
**Scenario**: Financial application with strict security and compliance requirements.
**Solution**:
- Private VPC design with no internet access
- Interface VPC Endpoints for AWS services
- Transit Gateway with fine-grained route control
- NACLs and Security Groups for defense in depth
- CloudFront with field-level encryption for sensitive data
- WAF for application protection
- Private Link for third-party service access

### Scalable Content Delivery
**Scenario**: Media streaming platform with global audience and varying demand.
**Solution**:
- CloudFront with Origin Shield
- S3 Transfer Acceleration for uploads
- MediaPackage for video preparation
- Lambda@Edge for personalization
- Route 53 latency routing
- ALB with auto-scaling for origin infrastructure

## Network Security Best Practices

### Defense in Depth
- VPC design with public, private, and isolated subnets
- NACLs for subnet-level control
- Security Groups for instance-level control
- Host-based firewalls and network monitoring
- WAF for application protection
- Shield for DDoS protection

### Traffic Inspection
- VPC Traffic Mirroring
- Gateway Load Balancer for transparent inspection
- VPC Flow Logs for traffic analysis
- Network Firewall for managed firewall
- Third-party security appliances

### Encryption in Transit
- TLS termination at load balancers
- End-to-end encryption with re-encryption
- VPN for secure transit
- Private connectivity with PrivateLink
- Field-level encryption for sensitive data

## Implementation Best Practices

### VPC Design Principles
- Plan IP addressing for growth
- Segment by function and security level
- Design for availability across AZs
- Implement least-privilege network controls
- Document network flows and security controls

### Load Balancer Selection
- Choose based on protocol and feature requirements
- Consider performance needs and connection rates
- Evaluate security requirements
- Plan for zonal failures
- Configure health checks appropriately

### Connectivity Options
- Evaluate bandwidth, latency, and reliability requirements
- Implement appropriate redundancy
- Consider cost implications of data transfer
- Document failover procedures
- Test connectivity scenarios regularly

## Exam Tips and Common Pitfalls

### Key Points to Remember
- VPC peering is non-transitive
- Transit Gateway supports transitive routing
- Direct Connect is not automatically redundant
- NACLs are stateless, Security Groups are stateful
- CloudFront caches based on Cache Key
- Load balancers have different connection limits and features

### Common Mistakes
- Overlooking IP address overlap in VPC peering
- Assuming automatic redundancy for Direct Connect
- Not accounting for zonal isolation with NLB
- Configuring overly permissive security groups
- Misunderstanding routing prioritization
- Not planning for regional failures in global applications

## Additional Resources
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [AWS Networking & Content Delivery Blog](https://aws.amazon.com/blogs/networking-and-content-delivery/)
- [VPC Design Best Practices Whitepaper](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/aws-vpc-connectivity-options.pdf)

---
*Return to [Main Study Guide](../aws_solutions_architect_pro_study_guide.md)* 