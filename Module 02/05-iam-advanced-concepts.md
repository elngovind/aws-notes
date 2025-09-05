# IAM Advanced Concepts

## MyLearning.com's Advanced IAM Journey

### The Scaling Challenge (September 2021)
As MyLearning.com grew from 50,000 to 200,000 users, their team expanded to 50 employees across 5 countries. The basic IAM setup that worked for 10 people was no longer sufficient.

```
Scaling Challenges Faced:
├── Team Growth Issues
│   ├── 50+ employees needing AWS access
│   ├── Multiple contractors and consultants
│   ├── Different roles and responsibilities
│   └── Varying access requirements
├── Multi-Account Complexity
│   ├── 6 AWS accounts (prod, staging, dev, etc.)
│   ├── Cross-account access requirements
│   ├── Centralized identity management needs
│   └── Consistent policy enforcement
├── Compliance Requirements
│   ├── SOC 2 Type II certification
│   ├── GDPR compliance for EU users
│   ├── Educational data privacy (COPPA)
│   └── Financial data security (PCI DSS)
├── Operational Overhead
│   ├── Manual user provisioning
│   ├── Permission creep over time
│   ├── Difficult access reviews
│   └── Inconsistent security policies
└── Security Concerns
    ├── Over-privileged users
    ├── Shared service accounts
    ├── Lack of temporary access
    └── Insufficient access monitoring
```

## Advanced IAM Components

### 1. Permission Boundaries

#### Definition
A permissions boundary is an advanced feature for using a managed policy to set the maximum permissions that an identity-based policy can grant to an IAM entity.

#### MyLearning.com's Permission Boundary Strategy
```
Permission Boundary Implementation:
├── Developer Boundary Policy
│   ├── Maximum permissions for developers
│   ├── Prevents privilege escalation
│   ├── Allows development activities only
│   └── Blocks production resource access
├── Contractor Boundary Policy
│   ├── Restricted access for external users
│   ├── Time-limited permissions
│   ├── Project-specific resource access
│   └── Enhanced monitoring requirements
├── Service Boundary Policy
│   ├── Application service limitations
│   ├── Prevents lateral movement
│   ├── Resource type restrictions
│   └── Cost control mechanisms
└── Admin Boundary Policy
    ├── Even admins have boundaries
    ├── Prevents accidental deletions
    ├── Requires additional approval for sensitive operations
    └── Audit trail requirements
```

#### Developer Permission Boundary Example
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowedServices",
      "Effect": "Allow",
      "Action": [
        "ec2:*",
        "s3:*",
        "rds:*",
        "lambda:*",
        "cloudwatch:*",
        "logs:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:RequestedRegion": [
            "ap-south-1",
            "ap-southeast-1"
          ]
        }
      }
    },
    {
      "Sid": "DenyProductionAccess",
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/Environment": "production"
        }
      }
    },
    {
      "Sid": "DenyIAMModification",
      "Effect": "Deny",
      "Action": [
        "iam:CreateUser",
        "iam:DeleteUser",
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:AttachUserPolicy",
        "iam:DetachUserPolicy",
        "iam:PutUserPermissionsBoundary",
        "iam:DeleteUserPermissionsBoundary"
      ],
      "Resource": "*"
    },
    {
      "Sid": "DenyBillingAccess",
      "Effect": "Deny",
      "Action": [
        "aws-portal:*",
        "budgets:*",
        "ce:*"
      ],
      "Resource": "*"
    },
    {
      "Sid": "RestrictInstanceTypes",
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": "arn:aws:ec2:*:*:instance/*",
      "Condition": {
        "ForAllValues:StringNotEquals": {
          "ec2:InstanceType": [
            "t2.micro",
            "t2.small",
            "t3.micro",
            "t3.small",
            "t3.medium"
          ]
        }
      }
    }
  ]
}
```

#### How Permission Boundaries Work
```
Permission Boundary Logic:
├── Identity-Based Policy (What user can do)
│   ├── Grants: S3 full access, EC2 full access
│   ├── Allows: All S3 and EC2 operations
│   └── No restrictions on resources
├── Permission Boundary (Maximum allowed)
│   ├── Limits: Only development resources
│   ├── Denies: Production environment access
│   └── Restricts: Instance types and regions
├── Effective Permissions (Intersection)
│   ├── Result: S3 and EC2 access to dev resources only
│   ├── Blocked: Production resource access
│   └── Limited: To allowed instance types
└── Business Benefit
    ├── Developers can work independently
    ├── Cannot accidentally affect production
    ├── Automatic compliance with policies
    └── Reduced security risks
```

### 2. Cross-Account Access

#### Definition
Cross-account access allows users or applications in one AWS account to access resources in another AWS account.

#### MyLearning.com's Multi-Account Architecture
```
Cross-Account Access Strategy:
├── Master Account (111111111111)
│   ├── Billing and cost management
│   ├── Organization management
│   ├── Centralized logging
│   └── Cross-account role definitions
├── Production Account (222222222222)
│   ├── Live applications and data
│   ├── Customer-facing services
│   ├── Strict access controls
│   └── High availability setup
├── Staging Account (333333333333)
│   ├── Pre-production testing
│   ├── Integration testing
│   ├── Performance testing
│   └── Security testing
├── Development Account (444444444444)
│   ├── Development environments
│   ├── Feature development
│   ├── Experimentation
│   └── Learning environments
├── Security Account (555555555555)
│   ├── Centralized logging (CloudTrail)
│   ├── Security monitoring tools
│   ├── Compliance reporting
│   └── Incident response
└── Data Account (666666666666)
    ├── Data lake and warehousing
    ├── Analytics and ML workloads
    ├── Business intelligence
    └── Reporting systems
```

#### Cross-Account Role Example
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::444444444444:user/developer1",
          "arn:aws:iam::444444444444:user/developer2",
          "arn:aws:iam::444444444444:role/DevOps-Role"
        ]
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "MyLearning-Unique-ID-2021"
        },
        "Bool": {
          "aws:MultiFactorAuthPresent": "true"
        },
        "DateGreaterThan": {
          "aws:CurrentTime": "2021-01-01T00:00:00Z"
        },
        "DateLessThan": {
          "aws:CurrentTime": "2024-12-31T23:59:59Z"
        },
        "IpAddress": {
          "aws:SourceIp": [
            "203.0.113.0/24",
            "198.51.100.0/24"
          ]
        }
      }
    }
  ]
}
```

#### Cross-Account Access Implementation
```
Cross-Account Setup Process:
├── Step 1: Create Role in Target Account
│   ├── Define trust policy
│   ├── Specify allowed principals
│   ├── Set conditions (MFA, IP, etc.)
│   └── Add external ID for security
├── Step 2: Attach Permissions Policy
│   ├── Define what role can do
│   ├── Apply principle of least privilege
│   ├── Use resource-level permissions
│   └── Set time-based restrictions
├── Step 3: Grant AssumeRole Permission
│   ├── Allow users/roles to assume role
│   ├── Add to identity-based policies
│   ├── Consider permission boundaries
│   └── Document access patterns
├── Step 4: Implement Role Switching
│   ├── AWS CLI profile configuration
│   ├── Console role switching setup
│   ├── Application SDK configuration
│   └── Automated role assumption
└── Step 5: Monitor and Audit
    ├── CloudTrail logging
    ├── Access pattern analysis
    ├── Regular access reviews
    └── Automated compliance checks
```

### 3. Identity Federation

#### Definition
Identity federation allows users to access AWS resources using credentials from external identity providers like Active Directory, Google Workspace, or SAML providers.

#### MyLearning.com's Federation Strategy
```
Identity Federation Architecture:
├── Google Workspace Integration
│   ├── 200+ employees with Google accounts
│   ├── Single sign-on experience
│   ├── Automatic user provisioning
│   └── Group-based access mapping
├── Active Directory Federation
│   ├── On-premises AD integration
│   ├── Corporate laptop access
│   ├── Windows authentication
│   └── Seamless AWS access
├── GitHub Actions Integration
│   ├── CI/CD pipeline authentication
│   ├── Temporary credential access
│   ├── Repository-based permissions
│   └── Automated deployments
└── SAML Provider Integration
    ├── Third-party identity providers
    ├── Customer access (B2B features)
    ├── Partner integrations
    └── Compliance requirements
```

#### SAML Federation Setup
```
SAML Federation Configuration:
├── Identity Provider Setup
│   ├── Configure SAML IdP (Google, Okta, etc.)
│   ├── Define user attributes
│   ├── Set up group mappings
│   └── Configure assertion settings
├── AWS SAML Provider Creation
│   ├── Upload IdP metadata
│   ├── Create SAML provider in IAM
│   ├── Configure trust relationships
│   └── Set up attribute mappings
├── Role Configuration
│   ├── Create roles for federated users
│   ├── Define trust policy for SAML
│   ├── Map IdP groups to AWS roles
│   └── Set session duration limits
├── Application Integration
│   ├── Configure SAML assertions
│   ├── Set up attribute-based access
│   ├── Implement role selection logic
│   └── Handle session management
└── Testing and Validation
    ├── Test user authentication flow
    ├── Verify role assumptions
    ├── Check attribute mappings
    └── Validate security controls
```

#### Web Identity Federation Example
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "accounts.google.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "accounts.google.com:aud": "123456789012.apps.googleusercontent.com",
          "accounts.google.com:hd": "mylearning.com"
        },
        "ForAllValues:StringLike": {
          "accounts.google.com:email": "*@mylearning.com"
        }
      }
    }
  ]
}
```

### 4. Service Control Policies (SCPs)

#### Definition
Service Control Policies (SCPs) are a type of organization policy that you can use to manage permissions in your organization at the organizational unit (OU) or account level.

#### MyLearning.com's SCP Strategy
```
SCP Organizational Structure:
├── Root Organization
│   ├── Security OU
│   │   ├── Security Account
│   │   ├── Logging Account
│   │   └── Compliance Account
│   ├── Production OU
│   │   ├── Production Account
│   │   ├── Staging Account
│   │   └── DR Account
│   ├── Development OU
│   │   ├── Development Account
│   │   ├── Testing Account
│   │   └── Sandbox Account
│   └── Shared Services OU
│       ├── Networking Account
│       ├── DNS Account
│       └── Monitoring Account
```

#### Production Environment SCP
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyRootAccess",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:PrincipalType": "Root"
        }
      }
    },
    {
      "Sid": "DenyRegionRestriction",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": [
            "ap-south-1",
            "ap-southeast-1",
            "us-east-1"
          ]
        }
      }
    },
    {
      "Sid": "DenyInstanceTypeRestriction",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "ec2:RunInstances",
      "Resource": "arn:aws:ec2:*:*:instance/*",
      "Condition": {
        "ForAllValues:StringNotEquals": {
          "ec2:InstanceType": [
            "t3.medium",
            "t3.large",
            "t3.xlarge",
            "m5.large",
            "m5.xlarge",
            "c5.large",
            "c5.xlarge"
          ]
        }
      }
    },
    {
      "Sid": "RequireEncryption",
      "Effect": "Deny",
      "Principal": "*",
      "Action": [
        "s3:PutObject",
        "rds:CreateDBInstance",
        "rds:CreateDBCluster"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    },
    {
      "Sid": "PreventConfigChanges",
      "Effect": "Deny",
      "Principal": "*",
      "Action": [
        "config:DeleteConfigRule",
        "config:DeleteConfigurationRecorder",
        "config:DeleteDeliveryChannel",
        "config:StopConfigurationRecorder"
      ],
      "Resource": "*"
    }
  ]
}
```

### 5. Access Analyzer

#### Definition
AWS IAM Access Analyzer helps identify resources in your organization and accounts that are shared with an external entity.

#### MyLearning.com's Access Analyzer Implementation
```
Access Analyzer Configuration:
├── Organization-wide Analysis
│   ├── Cross-account resource sharing
│   ├── Public resource identification
│   ├── External access detection
│   └── Policy validation
├── Account-level Analysis
│   ├── S3 bucket policies
│   ├── IAM role trust policies
│   ├── KMS key policies
│   └── Lambda resource policies
├── Automated Monitoring
│   ├── Daily analysis reports
│   ├── Alert on new findings
│   ├── Integration with security tools
│   └── Compliance dashboard updates
└── Remediation Workflow
    ├── Finding categorization
    ├── Risk assessment
    ├── Automated remediation
    └── Manual review process
```

#### Access Analyzer Findings Example
```
Typical Findings at MyLearning.com:
├── S3 Bucket Findings
│   ├── Bucket shared with external account
│   ├── Public read access detected
│   ├── Cross-account replication permissions
│   └── CloudFront OAI access
├── IAM Role Findings
│   ├── Role assumable by external account
│   ├── Overly permissive trust policy
│   ├── Unused cross-account access
│   └── Service role misconfigurations
├── KMS Key Findings
│   ├── Key shared with external account
│   ├── Public key policy detected
│   ├── Cross-account encryption access
│   └── Service-linked key sharing
└── Lambda Function Findings
    ├── Function invokable externally
    ├── Resource-based policy issues
    ├── Cross-account function access
    └── API Gateway integration permissions
```

### 6. Temporary Credentials and STS

#### Definition
AWS Security Token Service (STS) enables you to request temporary, limited-privilege credentials for IAM users or federated users.

#### MyLearning.com's STS Usage Patterns
```
STS Implementation Scenarios:
├── Cross-Account Access
│   ├── Developers accessing staging from dev account
│   ├── CI/CD pipelines deploying across accounts
│   ├── Backup services accessing multiple accounts
│   └── Monitoring tools collecting cross-account data
├── Federated Access
│   ├── Google Workspace users accessing AWS
│   ├── GitHub Actions assuming deployment roles
│   ├── SAML users from corporate directory
│   └── Web identity federation for mobile apps
├── Application Access
│   ├── EC2 instances assuming service roles
│   ├── Lambda functions with execution roles
│   ├── ECS tasks with task roles
│   └── Kubernetes pods with service accounts
├── Temporary Access
│   ├── Contractor access for specific projects
│   ├── Emergency access during incidents
│   ├── Audit access for compliance reviews
│   └── Training access for new employees
└── Enhanced Security
    ├── MFA-protected operations
    ├── Time-limited access tokens
    ├── IP-restricted access
    └── Condition-based permissions
```

#### STS Token Configuration
```
STS Token Parameters:
├── Session Duration
│   ├── Minimum: 15 minutes
│   ├── Maximum: 12 hours (roles)
│   ├── Maximum: 36 hours (federated users)
│   └── Default: 1 hour
├── Session Name
│   ├── Identifies the session
│   ├── Appears in CloudTrail logs
│   ├── Useful for auditing
│   └── Required for assume role
├── External ID
│   ├── Additional security measure
│   ├── Prevents confused deputy problem
│   ├── Required for cross-account access
│   └── Unique identifier per integration
├── MFA Requirements
│   ├── MFA serial number
│   ├── MFA token code
│   ├── Time-based validation
│   └── Enhanced security for sensitive operations
└── Policy Restrictions
    ├── Session policy (additional restrictions)
    ├── Cannot expand permissions
    ├── Intersection with role permissions
    └── Temporary permission reduction
```

## Advanced IAM Monitoring and Compliance

### MyLearning.com's IAM Governance Framework
```
IAM Governance Implementation:
├── Access Reviews
│   ├── Quarterly user access reviews
│   ├── Monthly privileged access reviews
│   ├── Annual role and policy reviews
│   └── Continuous automated monitoring
├── Compliance Monitoring
│   ├── SOC 2 compliance checks
│   ├── GDPR access controls
│   ├── PCI DSS requirements
│   └── Educational data privacy (COPPA)
├── Automated Remediation
│   ├── Unused access key rotation
│   ├── Inactive user deactivation
│   ├── Over-privileged access alerts
│   └── Policy drift detection
├── Reporting and Analytics
│   ├── Access pattern analysis
│   ├── Permission usage reports
│   ├── Security posture dashboards
│   └── Compliance status tracking
└── Incident Response
    ├── Compromised credential procedures
    ├── Unauthorized access investigation
    ├── Privilege escalation detection
    └── Forensic analysis capabilities
```

### IAM Security Metrics
```
Key Security Metrics Tracked:
├── User Management
│   ├── Active users vs total users
│   ├── Users with console access
│   ├── Users with programmatic access
│   ├── Users without MFA enabled
│   └── Inactive users (>90 days)
├── Access Patterns
│   ├── Failed login attempts
│   ├── Unusual access patterns
│   ├── Cross-account access frequency
│   ├── Privileged operation usage
│   └── After-hours access attempts
├── Policy Management
│   ├── Policies with wildcard permissions
│   ├── Inline policies vs managed policies
│   ├── Unused policies and roles
│   ├── Policy version management
│   └── Permission boundary coverage
├── Compliance Status
│   ├── MFA adoption rate
│   ├── Password policy compliance
│   ├── Access key rotation status
│   ├── Role assumption patterns
│   └── External access findings
└── Security Events
    ├── Root account usage
    ├── Policy modification events
    ├── User creation/deletion events
    ├── Role assumption failures
    └── Permission escalation attempts
```

---

*Next: Exploring different methods to interact with AWS services, from console to Infrastructure as Code, that MyLearning.com uses for automation and efficiency.*