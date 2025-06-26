# AWS Identity and Access Management (IAM)

## Overview
Identity and Access Management (IAM) is a critical service for securing your AWS environment. It enables you to manage access to AWS services and resources securely by controlling who is authenticated and authorized to use resources. IAM provides a centralized view of who and what are allowed inside your AWS account (authentication) and who and what have permissions to use your AWS resources (authorization).

### Key IAM Features
- **Global Service**: IAM is not region-specific; your identities and policies work across all AWS regions
- **Integrated with AWS Services**: IAM works with most AWS services by default
- **Shared Access**: Share your AWS account and resources without sharing your credentials
- **Granular Permissions**: Provide specific permissions to specific identities for specific resources
- **Multi-factor Authentication (MFA)**: Add an additional layer of security to your account
- **Identity Federation**: Allow users with existing credentials elsewhere to access your AWS resources
- **Free Service**: IAM is included at no additional charge with your AWS account

## Key Concepts

### IAM Identities
| Identity Type | Description | Use Cases |
|--------------|-------------|-----------|
| Users | Individual identities with permanent credentials | Named persons or applications requiring dedicated credentials |
| Groups | Collection of IAM users | Organizing users by job function or department |
| Roles | Set of permissions assumed by users or services | Cross-account access, temporary access, service-to-service |

### IAM User Credentials
When you create an IAM user, you can provide two types of access:

1. **Console Access**: A username and password to sign in to the AWS Management Console
   - Password policies can enforce complexity requirements and rotation periods
   - Credentials remain valid until explicitly changed or revoked

2. **Programmatic Access**: Access keys for AWS CLI, SDK, or API requests
   - Consists of an access key ID and secret access key
   - Should be rotated regularly
   - Can be created, deactivated, or deleted as needed

Best practice is to avoid creating individual users for each person. Instead, use identity federation or IAM roles where possible, especially in enterprise environments.

### Permission Management
- **Policies**: JSON documents defining permissions
- **Permission Boundaries**: Limit maximum permissions an identity can have
- **Service Control Policies (SCPs)**: Limit permissions for entire AWS accounts or OUs
- **Access Control Lists (ACLs)**: Grant permissions to specific AWS resources
- **Session Policies**: Temporary policies for assumed roles

### Policy Evaluation Logic
1. Default **Deny**
2. Evaluate if an explicit **Deny** exists
3. Evaluate if an explicit **Allow** exists
4. If no explicit Allow, default to **Deny**

## Policy Types

### Identity-Based Policies
- **AWS Managed Policies**: Created and managed by AWS
- **Customer Managed Policies**: Created and managed by you
- **Inline Policies**: Embedded directly in a user, group, or role

### Resource-Based Policies
- Attached directly to resources (e.g., S3 bucket policies, KMS key policies)
- Control who can access the resource
- Can allow cross-account access

### Policy Structure
IAM policies use JSON format and contain the following required elements:

| Element | Description | Required | Example |
|---------|-------------|----------|---------|
| Version | Policy language version (use "2012-10-17") | ✔ | `"Version": "2012-10-17"` |
| Statement | Container for policy elements | ✔ | `"Statement": [{ ... }]` |
| Effect | Whether to Allow or Deny | ✔ | `"Effect": "Allow"` |
| Action | Specific actions to allow/deny | ✔ | `"Action": "s3:GetObject"` |
| Resource | Resources the actions apply to | ✔ | `"Resource": "arn:aws:s3:::example-bucket/*"` |
| Condition | Conditions for when policy applies | ✖ | `"Condition": {"IpAddress": {"aws:SourceIp": "203.0.113.0/24"}}` |
| Principal | Entity that gets the permission (used in resource-based policies) | ✖ | `"Principal": {"AWS": "arn:aws:iam::123456789012:user/Bob"}` |

#### Example Administrative Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }]
}
```

#### Example User-Specific Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "iam:ChangePassword",
      "iam:GetUser"
    ],
    "Resource": "arn:aws:iam::123456789012:user/${aws:username}"
  }]
}
```

### Permission Boundaries
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*",
        "ec2:*",
        "dynamodb:*"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": [
        "organizations:*",
        "iam:DeleteRole",
        "iam:DeleteUser"
      ],
      "Resource": "*"
    }
  ]
}
```

## Advanced IAM Features

### Cross-Account Access
- Use IAM roles with trust relationships
- Resource-based policies with principal elements
- Role chaining (assuming roles across accounts)

### Identity Federation
| Federation Type | Use Case | Implementation |
|----------------|----------|---------------|
| AWS IAM Identity Center | Enterprise SSO | Centralized access to multiple AWS accounts |
| SAML 2.0 | Enterprise IdP integration | Connect to Active Directory, Okta, etc. |
| Web Identity Federation | Consumer applications | Google, Facebook, Amazon, OIDC providers |
| Custom Identity Broker | Complex identity requirements | Custom solution using STS |

### Temporary Security Credentials
- Generated by AWS Security Token Service (STS)
- Operations: `AssumeRole`, `AssumeRoleWithSAML`, `AssumeRoleWithWebIdentity`
- Benefits: No long-term credentials, automatic expiration, revocable

### Service Roles
- Roles assumed by AWS services
- Service-linked roles (pre-defined by AWS)
- Necessary for services to perform actions on your behalf

## Common Exam Scenarios

### Multi-Account Management
**Scenario**: Design an IAM strategy for an enterprise with hundreds of AWS accounts.
**Solution**:
- Implement AWS Organizations with organizational units (OUs)
- Use Service Control Policies (SCPs) for guardrails
- Deploy IAM Identity Center for centralized access management
- Establish role-based access with least privilege
- Implement cross-account roles for administrative access

### Secure Application Access
**Scenario**: Design secure access for applications running on EC2 to access S3 and DynamoDB.
**Solution**:
- Create IAM roles with specific permissions to S3 and DynamoDB
- Use instance profiles to attach roles to EC2 instances
- Implement least privilege by restricting access to specific resources
- Use conditions to limit access by IP address, time of day, etc.
- Enable CloudTrail for auditing

### Hybrid Identity Management
**Scenario**: Integrate corporate Active Directory with AWS.
**Solution**:
- Set up SAML federation between AWS and on-premises identity provider
- Configure IAM roles mapped to AD groups
- Implement IAM Identity Center for seamless SSO
- Use AWS Managed Microsoft AD or AD Connector
- Setup CloudWatch for monitoring authentication events

### Secure API Access
**Scenario**: Design secure access for mobile applications using AWS APIs.
**Solution**:
- Implement Amazon Cognito for user authentication
- Use Amazon Cognito identity pools to provide temporary AWS credentials
- Configure fine-grained IAM policies with conditions
- Implement API Gateway with Cognito authorizers
- Enable AWS WAF for request filtering

## IAM Security Best Practices

### Root User Security
- **Lock Down the Root User**: The root user has complete access to all resources in your account
- **Avoid Using Root**: Use only for tasks that specifically require root access
- **Delete Root Access Keys**: Do not create or use root account access keys
- **Enable MFA**: Always enable MFA on the root account
- **Use Strong Password**: Create a complex, unique password for the root user

### Authentication Best Practices
- Enable MFA for all users, especially privileged users
- Enforce strong password policies
- Rotate credentials regularly
- Use temporary security credentials instead of long-term access keys
- Use IAM roles for EC2 instances instead of storing credentials

### Authorization Best Practices
- **Principle of Least Privilege**: Grant only permissions required for specific tasks
- Use permission boundaries for delegation
- Regularly review and remove unused permissions
- Use conditions in IAM policies to restrict access
- Implement just-in-time access for privileged operations
- Use groups to assign permissions to users

### Monitoring and Auditing
- Enable CloudTrail for all accounts
- Configure CloudWatch alarms for suspicious activities
- Regularly review CloudTrail logs
- Use IAM Access Analyzer to identify unintended access
- Implement AWS Config rules for compliance monitoring

### Identity Federation
- Use federation for enterprise users rather than creating IAM users
- Consider AWS IAM Identity Center (successor to AWS SSO) for managing access
- Centralize identity management in a single location
- Use IAM roles to provide permissions to federated users

## AWS IAM Identity Center (formerly AWS SSO)
AWS IAM Identity Center provides a centralized place to manage access to multiple AWS accounts and applications. Key benefits include:

- **Single Sign-On**: Users sign in once to access all assigned accounts and applications
- **Centralized Management**: Manage user access to all accounts from one place
- **Directory Integration**: Connect to your existing identity source (AWS Managed Directory, Active Directory, external IdP)
- **Fine-grained Permissions**: Assign custom permission sets to users and groups
- **Account Independence**: Access management is not dependent on your identity provider
- **Reduced Administrative Overhead**: No need to create and manage IAM users across multiple accounts

When to use IAM Identity Center:
- Organizations with multiple AWS accounts
- Need for centralized identity management
- Integrating with an existing corporate directory
- Implementing single sign-on across your organization

## Common IAM Policy Examples

### S3 Bucket Access with Conditions
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::example-bucket/*",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        },
        "StringLike": {
          "s3:prefix": "projects/${aws:username}/*"
        }
      }
    }
  ]
}
```

### Cross-Account Role Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:root"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "aws:PrincipalOrgID": "o-exampleorgid"
        }
      }
    }
  ]
}
```

### Dynamic Permissions Based on Tags
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/Department": "${aws:PrincipalTag/Department}"
        }
      }
    }
  ]
}
```

## IAM Integration with Other Services

### AWS Organizations
- Manage multiple AWS accounts centrally
- Apply SCPs to restrict member accounts
- Implement tag policies for consistent tagging
- Configure service control using Service Access Policies
- Centralize CloudTrail logging and AWS Config

### AWS CloudTrail
- Log all IAM actions for auditing
- Investigate security incidents
- Monitor user activity
- Track policy changes
- Compliance verification

### AWS Config
- Continuously monitor IAM configuration
- Implement rules for compliance
- Evaluate resource configurations
- Remediate non-compliant resources
- Track configuration history

### AWS KMS
- Control encryption key access
- Implement key policies
- Define administrative and usage permissions
- Enforce encryption with IAM conditions
- Implement key rotation policies

## Exam Tips and Common Pitfalls

### Key Points to Remember
- IAM is global (not region-specific)
- Root user should only be used for initial setup and emergency access
- Always apply least privilege principles
- Policy evaluation always starts with a default deny
- Explicit denies always override allows
- IAM does not directly control access to data within resources (e.g., data in an S3 object)

### Common Mistakes
- Using overly permissive policies
- Relying on long-term access keys instead of roles
- Ignoring permission boundaries for delegation
- Not implementing MFA for privileged users
- Failing to regularly review and rotate credentials
- Not considering the policy evaluation order

## Practical Implementation Guide

### IAM Implementation Checklist
- [ ] Establish account structure with AWS Organizations
- [ ] Configure identity federation for corporate users
- [ ] Implement role-based access control
- [ ] Define resource-based policies for cross-account access
- [ ] Enable MFA for all users
- [ ] Configure permission boundaries for delegated administration
- [ ] Implement monitoring and alerting
- [ ] Document access management procedures
- [ ] Regularly review permissions

### Recommended Architecture for Enterprise
```
AWS Organizations
├── Management Account
│   └── Security Tooling
├── Security Account
│   └── Identity Provider Integration
├── Shared Services Account
│   └── Centralized Logging
├── Development OU
│   ├── Dev Account
│   └── Test Account
└── Production OU
    ├── Prod Account
    └── DR Account
```

## Resources for Further Learning
- [AWS IAM Documentation](https://docs.aws.amazon.com/IAM/)
- [IAM Best Practices Whitepaper](https://d1.awsstatic.com/whitepapers/aws-iam-best-practices.pdf)
- [AWS Security Blog](https://aws.amazon.com/blogs/security/)
- [AWS Well-Architected Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)
- [AWS IAM Identity Center Documentation](https://docs.aws.amazon.com/singlesignon/latest/userguide/what-is.html)

---
*Return to [Main Study Guide](../aws_solutions_architect_pro_study_guide.md)* 