# AWS Identity & Access Management (IAM) Fundamentals

## The Security Wake-up Call: MyLearning.com's IAM Journey

### The Incident That Changed Everything (March 2021)
After the cryptocurrency mining attack that cost ₹45,000, MyLearning.com realized they needed to completely overhaul their security approach. This incident became their catalyst for mastering AWS IAM.

```
Post-Incident Analysis:
├── Root Cause
│   ├── Shared root account credentials
│   ├── No multi-factor authentication
│   ├── Overly permissive access
│   └── No access monitoring
├── Immediate Impact
│   ├── ₹45,000 unexpected charges
│   ├── Service disruption
│   ├── Customer trust issues
│   └── Team productivity loss
├── Long-term Consequences
│   ├── Security reputation damage
│   ├── Compliance concerns
│   ├── Investor confidence impact
│   └── Operational overhead
└── Lessons Learned
    ├── Security is not optional
    ├── Principle of least privilege
    ├── Monitoring is essential
    └── Education is crucial
```

## What is AWS IAM?

### Definition
AWS Identity and Access Management (IAM) is a web service that helps you securely control access to AWS resources. You use IAM to control who is authenticated (signed in) and authorized (has permissions) to use resources.

### MyLearning.com's IAM Transformation
```
Before IAM Implementation:
├── Single root account for everything
├── Shared passwords via email/Slack
├── No access controls or restrictions
├── No audit trail of actions
└── No way to revoke specific access

After IAM Implementation:
├── Individual user accounts for each team member
├── Role-based access control
├── Principle of least privilege
├── Complete audit trail via CloudTrail
└── Granular permission management
```

## Core IAM Components

### 1. IAM Users

#### Definition
An IAM user is an entity that you create in AWS to represent the person or application that uses it to interact with AWS.

#### MyLearning.com's User Strategy
```
User Account Structure:
├── Individual Users (Team Members)
│   ├── raj.kumar@mylearning.com (CTO)
│   ├── priya.sharma@mylearning.com (CPO)
│   ├── amit.patel@mylearning.com (CEO)
│   ├── dev1@mylearning.com (Developer)
│   ├── dev2@mylearning.com (Developer)
│   └── ops@mylearning.com (Operations)
├── Service Users (Applications)
│   ├── mylearning-app-prod
│   ├── mylearning-app-staging
│   ├── mylearning-backup-service
│   └── mylearning-monitoring
├── Contractor Users (Temporary)
│   ├── contractor1@external.com
│   ├── consultant@agency.com
│   └── auditor@compliance.com
└── Emergency Users (Break-glass)
    ├── emergency-admin
    ├── incident-response
    └── compliance-audit
```

#### User Creation Process
```
IAM User Creation Steps:
├── Step 1: User Details
│   ├── Username (unique identifier)
│   ├── Access type selection
│   ├── Console password (if needed)
│   └── Programmatic access (if needed)
├── Step 2: Permissions
│   ├── Add to groups
│   ├── Attach policies directly
│   ├── Copy permissions from existing user
│   └── Set permissions boundary
├── Step 3: Tags
│   ├── Department tag
│   ├── Role tag
│   ├── Project tag
│   └── Cost center tag
├── Step 4: Review
│   ├── Verify user details
│   ├── Check permissions
│   ├── Review tags
│   └── Create user
└── Step 5: Credentials
    ├── Download credentials CSV
    ├── Share securely with user
    ├── Force password change
    └── Enable MFA requirement
```

### 2. IAM Groups

#### Definition
An IAM group is a collection of IAM users. Groups let you specify permissions for multiple users, which can make it easier to manage permissions.

#### MyLearning.com's Group Structure
```
IAM Groups Organization:
├── Administrative Groups
│   ├── Administrators
│   │   ├── Full AWS access
│   │   ├── Members: Raj, Amit
│   │   ├── MFA required
│   │   └── IP restrictions
│   ├── PowerUsers
│   │   ├── All services except IAM
│   │   ├── Members: Senior developers
│   │   ├── Resource creation allowed
│   │   └── Cost center tagging required
│   └── ReadOnlyUsers
│       ├── View-only access
│       ├── Members: Auditors, consultants
│       ├── No resource modification
│       └── Billing information excluded
├── Development Groups
│   ├── Developers
│   │   ├── EC2, S3, RDS access
│   │   ├── Development environment only
│   │   ├── No production access
│   │   └── Resource limits applied
│   ├── DevOps
│   │   ├── CI/CD pipeline access
│   │   ├── Infrastructure management
│   │   ├── Monitoring tools access
│   │   └── Cross-environment deployment
│   └── QA-Testers
│       ├── Testing environment access
│       ├── Application deployment
│       ├── Test data management
│       └── Bug reporting tools
├── Business Groups
│   ├── Marketing
│   │   ├── S3 bucket for assets
│   │   ├── CloudFront management
│   │   ├── Analytics access
│   │   └── Cost reporting
│   ├── Finance
│   │   ├── Billing and cost management
│   │   ├── Budget creation and monitoring
│   │   ├── Reserved instance management
│   │   └── Cost optimization reports
│   └── Support
│       ├── CloudWatch logs access
│       ├── Support case management
│       ├── Customer data access (limited)
│       └── Incident response tools
└── External Groups
    ├── Contractors
    │   ├── Limited time access
    │   ├── Specific project resources
    │   ├── No sensitive data access
    │   └── Enhanced monitoring
    ├── Auditors
    │   ├── Read-only access
    │   ├── Compliance reporting
    │   ├── Security configuration review
    │   └── Audit trail access
    └── Partners
        ├── Shared resource access
        ├── API integration permissions
        ├── Data exchange capabilities
        └── Limited service access
```

#### Group Management Best Practices
```
Group Management Strategy:
├── Naming Convention
│   ├── Environment prefix (prod-, dev-, test-)
│   ├── Function description (developers, admins)
│   ├── Access level (readonly, poweruser)
│   └── Consistent formatting
├── Permission Assignment
│   ├── Assign permissions to groups, not users
│   ├── Use managed policies when possible
│   ├── Document group purpose and permissions
│   └── Regular permission reviews
├── Group Hierarchy
│   ├── Base groups with common permissions
│   ├── Specialized groups for specific needs
│   ├── Temporary groups for projects
│   └── Emergency access groups
└── Lifecycle Management
    ├── Regular group membership reviews
    ├── Automated group cleanup
    ├── Permission audit trails
    └── Group usage monitoring
```

### 3. IAM Roles

#### Definition
An IAM role is an IAM identity that you can create in your account that has specific permissions. It's similar to an IAM user but is not associated with a specific person.

#### MyLearning.com's Role Strategy
```
IAM Roles Architecture:
├── Service Roles
│   ├── EC2-S3-Access-Role
│   │   ├── Allows EC2 to access S3 buckets
│   │   ├── Used by web application servers
│   │   ├── Read/write to specific buckets
│   │   └── CloudWatch logging permissions
│   ├── Lambda-Execution-Role
│   │   ├── Basic Lambda execution permissions
│   │   ├── CloudWatch Logs access
│   │   ├── VPC access (if needed)
│   │   └── Service-specific permissions
│   ├── RDS-Monitoring-Role
│   │   ├── Enhanced monitoring for RDS
│   │   ├── CloudWatch metrics publishing
│   │   ├── Performance insights access
│   │   └── Automated backup permissions
│   └── ECS-Task-Role
│       ├── Container application permissions
│       ├── Service discovery access
│       ├── Parameter Store access
│       └── Application-specific resources
├── Cross-Account Roles
│   ├── Production-Access-Role
│   │   ├── Allows staging account access to prod
│   │   ├── Deployment permissions only
│   │   ├── Time-limited access
│   │   └── MFA required for assumption
│   ├── Backup-Role
│   │   ├── Cross-account backup access
│   │   ├── S3 replication permissions
│   │   ├── Snapshot creation rights
│   │   └── Disaster recovery access
│   └── Audit-Role
│       ├── Read-only access across accounts
│       ├── Compliance reporting permissions
│       ├── Log aggregation access
│       └── Security assessment rights
├── Federated Roles
│   ├── Google-SSO-Role
│   │   ├── Google Workspace integration
│   │   ├── Automatic user provisioning
│   │   ├── Group-based access mapping
│   │   └── Session duration limits
│   ├── SAML-Developer-Role
│   │   ├── Active Directory integration
│   │   ├── Corporate identity federation
│   │   ├── Attribute-based access control
│   │   └── Conditional access policies
│   └── GitHub-Actions-Role
│       ├── CI/CD pipeline access
│       ├── Deployment permissions
│       ├── Resource provisioning rights
│       └── Temporary credential access
└── Temporary Roles
    ├── Emergency-Admin-Role
    │   ├── Break-glass access for incidents
    │   ├── Full administrative permissions
    │   ├── Time-limited (4 hours max)
    │   └── Extensive logging and alerting
    ├── Contractor-Role
    │   ├── Project-specific access
    │   ├── Limited duration (contract period)
    │   ├── Resource restrictions
    │   └── Enhanced monitoring
    └── Training-Role
        ├── Learning environment access
        ├── Safe experimentation permissions
        ├── Cost-limited resources
        └── Automatic cleanup
```

#### Role vs User Decision Matrix
```
When to Use Roles vs Users:
┌─────────────────────────────────────────────────────────────┐
│  Use Case                │  IAM User    │  IAM Role         │
│  ───────────────────────│──────────────│───────────────────│
│  Human Access           │  ✓ Yes       │  ✗ No             │
│  Application Access     │  ✗ No        │  ✓ Yes            │
│  AWS Service Access     │  ✗ No        │  ✓ Yes            │
│  Cross-Account Access   │  ✗ No        │  ✓ Yes            │
│  Temporary Access       │  ✗ No        │  ✓ Yes            │
│  Federated Access       │  ✗ No        │  ✓ Yes            │
│  Long-term Credentials  │  ✓ Yes       │  ✗ No             │
│  Rotating Credentials   │  ✗ Difficult │  ✓ Automatic      │
└─────────────────────────────────────────────────────────────┘
```

### 4. IAM Policies

#### Definition
Policies are documents that define permissions. AWS evaluates these policies when an IAM principal (user or role) makes a request.

#### Policy Types
```
IAM Policy Categories:
├── AWS Managed Policies
│   ├── Created and maintained by AWS
│   ├── Regularly updated by AWS
│   ├── Best practices implemented
│   ├── Examples:
│   │   ├── AmazonS3ReadOnlyAccess
│   │   ├── PowerUserAccess
│   │   ├── ReadOnlyAccess
│   │   └── AdministratorAccess
│   └── MyLearning.com Usage: 60% of policies
├── Customer Managed Policies
│   ├── Created and maintained by you
│   ├── Customized for specific needs
│   ├── Version control available
│   ├── Examples:
│   │   ├── MyLearning-Developer-Policy
│   │   ├── MyLearning-S3-Access-Policy
│   │   ├── MyLearning-RDS-Admin-Policy
│   │   └── MyLearning-Billing-Policy
│   └── MyLearning.com Usage: 35% of policies
└── Inline Policies
    ├── Embedded directly in user/role/group
    ├── One-to-one relationship
    ├── Deleted when entity is deleted
    ├── Examples:
    │   ├── Emergency access policies
    │   ├── Temporary project permissions
    │   ├── Unique service configurations
    │   └── Exception-based access
    └── MyLearning.com Usage: 5% of policies
```

#### MyLearning.com's Custom Policies

##### Developer Policy Example
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EC2DevelopmentAccess",
      "Effect": "Allow",
      "Action": [
        "ec2:RunInstances",
        "ec2:TerminateInstances",
        "ec2:StartInstances",
        "ec2:StopInstances",
        "ec2:DescribeInstances",
        "ec2:DescribeImages",
        "ec2:DescribeKeyPairs",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeSubnets",
        "ec2:DescribeVpcs"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "ec2:InstanceType": [
            "t2.micro",
            "t2.small",
            "t3.micro",
            "t3.small"
          ]
        },
        "ForAllValues:StringLike": {
          "aws:RequestedRegion": "ap-south-1"
        }
      }
    },
    {
      "Sid": "S3DevelopmentBucketAccess",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::mylearning-dev-*",
        "arn:aws:s3:::mylearning-dev-*/*"
      ]
    },
    {
      "Sid": "RDSDevelopmentAccess",
      "Effect": "Allow",
      "Action": [
        "rds:DescribeDBInstances",
        "rds:DescribeDBClusters",
        "rds:CreateDBSnapshot",
        "rds:DescribeDBSnapshots"
      ],
      "Resource": "*",
      "Condition": {
        "StringLike": {
          "rds:db-tag/Environment": "development"
        }
      }
    },
    {
      "Sid": "DenyProductionAccess",
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringLike": {
          "aws:RequestedRegion": "*",
          "aws:ResourceTag/Environment": "production"
        }
      }
    }
  ]
}
```

##### S3 Bucket Policy Example
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowApplicationAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/MyLearning-App-Role"
      },
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::mylearning-content/*",
      "Condition": {
        "StringEquals": {
          "s3:x-amz-server-side-encryption": "AES256"
        }
      }
    },
    {
      "Sid": "AllowCloudFrontAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::mylearning-content/*"
    },
    {
      "Sid": "DenyInsecureConnections",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::mylearning-content",
        "arn:aws:s3:::mylearning-content/*"
      ],
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    }
  ]
}
```

## Policy Evaluation Logic

### How AWS Evaluates Permissions
```
Policy Evaluation Process:
├── Step 1: Authentication
│   ├── Verify identity (user/role)
│   ├── Check MFA requirements
│   ├── Validate session tokens
│   └── Confirm account status
├── Step 2: Context Collection
│   ├── Gather request details
│   ├── Identify requested resources
│   ├── Collect condition keys
│   └── Determine request context
├── Step 3: Policy Retrieval
│   ├── Identity-based policies
│   ├── Resource-based policies
│   ├── Permission boundaries
│   └── Service control policies
├── Step 4: Policy Evaluation
│   ├── Default: Implicit Deny
│   ├── Check for Explicit Deny
│   ├── Look for Explicit Allow
│   └── Apply condition logic
└── Step 5: Final Decision
    ├── Deny if any explicit deny
    ├── Allow if explicit allow exists
    ├── Deny if no explicit allow
    └── Log decision for audit
```

### MyLearning.com's Policy Evaluation Example
```
Real-world Scenario: Developer accessing S3
├── Request: s3:GetObject on mylearning-prod-bucket
├── Identity: Developer user in Developers group
├── Policies Applied:
│   ├── AWS Managed: AmazonS3ReadOnlyAccess (Allow)
│   ├── Customer Managed: MyLearning-Developer-Policy (Deny prod)
│   ├── Inline Policy: None
│   └── Resource Policy: Bucket policy (Allow with conditions)
├── Evaluation Result:
│   ├── Explicit Deny found in developer policy
│   ├── Deny overrides any Allow
│   ├── Access denied
│   └── CloudTrail logs the denial
└── Business Impact:
    ├── Prevents accidental production access
    ├── Maintains environment separation
    ├── Ensures compliance requirements
    └── Provides audit trail
```

## IAM Best Practices Implementation

### MyLearning.com's IAM Best Practices
```
Security Best Practices:
├── Principle of Least Privilege
│   ├── Start with minimal permissions
│   ├── Add permissions as needed
│   ├── Regular permission reviews
│   └── Remove unused permissions
├── Use Groups for Permission Management
│   ├── Assign permissions to groups
│   ├── Add users to appropriate groups
│   ├── Avoid direct user permissions
│   └── Maintain group documentation
├── Enable MFA for All Users
│   ├── Virtual MFA for regular users
│   ├── Hardware MFA for admins
│   ├── MFA required for sensitive operations
│   └── Regular MFA device audits
├── Rotate Credentials Regularly
│   ├── 90-day password rotation
│   ├── Access key rotation
│   ├── Automated rotation where possible
│   └── Emergency credential procedures
├── Use Roles for Applications
│   ├── No hardcoded credentials
│   ├── Temporary credentials only
│   ├── Service-specific roles
│   └── Cross-account access via roles
├── Monitor and Audit Access
│   ├── CloudTrail for all API calls
│   ├── Access Analyzer for permissions
│   ├── Regular access reviews
│   └── Automated compliance checks
└── Implement Strong Password Policies
    ├── Minimum 12 characters
    ├── Complexity requirements
    ├── Password history enforcement
    ├── Account lockout policies
```

---

*Next: Advanced IAM concepts including Permission Boundaries, Cross-Account Access, and Identity Federation that MyLearning.com implemented as they scaled.*