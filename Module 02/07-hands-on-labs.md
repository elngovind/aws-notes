# Hands-on Labs & Exercises

## Lab Overview
These hands-on labs follow MyLearning.com's actual implementation journey, allowing you to recreate their AWS setup step by step.

## Prerequisites
- AWS Free Tier account
- Basic command line knowledge
- Text editor (VS Code recommended)
- Git installed locally

## Lab 1: AWS Account Setup and Security Hardening

### Objective
Set up a secure AWS account following MyLearning.com's security best practices learned from their security incident.

### Duration: 45 minutes

### Steps

#### Step 1: Create AWS Account (10 minutes)
```bash
# 1. Go to https://aws.amazon.com/
# 2. Click "Create an AWS Account"
# 3. Fill in account details:
#    - Email: your-email@domain.com
#    - Password: Strong password (20+ characters)
#    - Account name: MyLearning-Lab-Account
# 4. Choose "Personal" account type
# 5. Add payment information
# 6. Verify phone number
# 7. Select Basic support plan
```

#### Step 2: Secure Root Account (15 minutes)
```bash
# 1. Enable MFA for root account
# - Go to IAM Console
# - Click on "My Security Credentials"
# - Assign MFA device (use Google Authenticator or similar)
# - Test MFA login

# 2. Create billing alerts
# - Go to Billing Console
# - Enable billing alerts
# - Create CloudWatch alarm for $10 threshold
```

#### Step 3: Create IAM Admin User (20 minutes)
```bash
# 1. Create IAM user
aws iam create-user --user-name lab-admin

# 2. Create admin group
aws iam create-group --group-name Administrators

# 3. Attach admin policy to group
aws iam attach-group-policy \
    --group-name Administrators \
    --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# 4. Add user to admin group
aws iam add-user-to-group \
    --group-name Administrators \
    --user-name lab-admin

# 5. Create access keys
aws iam create-access-key --user-name lab-admin

# 6. Create login profile
aws iam create-login-profile \
    --user-name lab-admin \
    --password "TempPassword123!" \
    --password-reset-required
```

### Verification
- [ ] Root account has MFA enabled
- [ ] Billing alerts are configured
- [ ] IAM admin user created successfully
- [ ] Admin user can log in to console
- [ ] Admin user has programmatic access

### Expected Output
```json
{
    "User": {
        "Path": "/",
        "UserName": "lab-admin",
        "UserId": "AIDACKCEVSQ6C2EXAMPLE",
        "Arn": "arn:aws:iam::123456789012:user/lab-admin",
        "CreateDate": "2021-03-01T12:00:00Z"
    }
}
```

## Lab 2: AWS CLI Configuration and Basic Operations

### Objective
Configure AWS CLI with multiple profiles and perform basic operations similar to MyLearning.com's setup.

### Duration: 30 minutes

### Steps

#### Step 1: Install and Configure AWS CLI (10 minutes)
```bash
# Install AWS CLI v2 (macOS)
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /

# Install AWS CLI v2 (Linux)
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Verify installation
aws --version

# Configure default profile
aws configure
# AWS Access Key ID: [Your Access Key]
# AWS Secret Access Key: [Your Secret Key]
# Default region name: ap-south-1
# Default output format: json
```

#### Step 2: Create Multiple Profiles (10 minutes)
```bash
# Create development profile
aws configure --profile mylearning-dev
# Use same credentials but different region: us-east-1

# Create staging profile
aws configure --profile mylearning-staging
# Use same credentials, region: ap-southeast-1

# Verify profiles
aws configure list-profiles

# Test profile usage
aws sts get-caller-identity --profile mylearning-dev
aws sts get-caller-identity --profile mylearning-staging
```

#### Step 3: Basic AWS Operations (10 minutes)
```bash
# List all regions
aws ec2 describe-regions --output table

# List availability zones in ap-south-1
aws ec2 describe-availability-zones --region ap-south-1

# Create S3 bucket (replace with unique name)
aws s3 mb s3://mylearning-lab-bucket-$(date +%s) --region ap-south-1

# List S3 buckets
aws s3 ls

# Upload a test file
echo "Hello MyLearning Lab!" > test-file.txt
aws s3 cp test-file.txt s3://your-bucket-name/

# List bucket contents
aws s3 ls s3://your-bucket-name/
```

### Verification
- [ ] AWS CLI installed and working
- [ ] Multiple profiles configured
- [ ] Can switch between profiles
- [ ] S3 bucket created successfully
- [ ] File uploaded to S3

## Lab 3: IAM Users, Groups, and Policies

### Objective
Implement MyLearning.com's IAM structure with users, groups, and custom policies.

### Duration: 60 minutes

### Steps

#### Step 1: Create IAM Groups (15 minutes)
```bash
# Create Developers group
aws iam create-group --group-name Developers

# Create DevOps group
aws iam create-group --group-name DevOps

# Create ReadOnly group
aws iam create-group --group-name ReadOnly

# Verify groups created
aws iam list-groups
```

#### Step 2: Create Custom Policies (20 minutes)
```bash
# Create developer policy file
cat > developer-policy.json << 'EOF'
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
                }
            }
        },
        {
            "Sid": "S3DevelopmentAccess",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::*-dev-*",
                "arn:aws:s3:::*-dev-*/*"
            ]
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
        }
    ]
}
EOF

# Create the policy
aws iam create-policy \
    --policy-name MyLearning-Developer-Policy \
    --policy-document file://developer-policy.json

# Attach policy to Developers group
aws iam attach-group-policy \
    --group-name Developers \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/MyLearning-Developer-Policy
```

#### Step 3: Create IAM Users (15 minutes)
```bash
# Create developer users
aws iam create-user --user-name dev-user-1
aws iam create-user --user-name dev-user-2

# Create DevOps user
aws iam create-user --user-name devops-user-1

# Add users to groups
aws iam add-user-to-group --group-name Developers --user-name dev-user-1
aws iam add-user-to-group --group-name Developers --user-name dev-user-2
aws iam add-user-to-group --group-name DevOps --user-name devops-user-1

# Create login profiles
aws iam create-login-profile \
    --user-name dev-user-1 \
    --password "DevPassword123!" \
    --password-reset-required

aws iam create-login-profile \
    --user-name dev-user-2 \
    --password "DevPassword123!" \
    --password-reset-required
```

#### Step 4: Test Permissions (10 minutes)
```bash
# Create access keys for testing
aws iam create-access-key --user-name dev-user-1

# Configure test profile (use the access keys from above)
aws configure --profile test-developer
# Enter the access keys for dev-user-1

# Test developer permissions
aws ec2 describe-instances --profile test-developer
aws s3 ls --profile test-developer

# Try to access production resources (should fail)
aws ec2 describe-instances \
    --profile test-developer \
    --filters "Name=tag:Environment,Values=production"
```

### Verification
- [ ] IAM groups created successfully
- [ ] Custom policy created and attached
- [ ] Users created and added to groups
- [ ] Developer can access allowed resources
- [ ] Developer cannot access production resources

## Lab 4: Multi-Region Infrastructure with Terraform

### Objective
Deploy MyLearning.com's multi-region infrastructure using Terraform.

### Duration: 90 minutes

### Steps

#### Step 1: Install Terraform (10 minutes)
```bash
# Install Terraform (macOS)
brew install terraform

# Install Terraform (Linux)
wget https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip
unzip terraform_1.6.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/

# Verify installation
terraform version
```

#### Step 2: Create Terraform Configuration (30 minutes)
```bash
# Create project directory
mkdir mylearning-infrastructure
cd mylearning-infrastructure

# Create main.tf
cat > main.tf << 'EOF'
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# Primary region provider
provider "aws" {
  alias  = "primary"
  region = "ap-south-1"
  
  default_tags {
    tags = {
      Environment = "lab"
      Project     = "MyLearning"
      ManagedBy   = "Terraform"
    }
  }
}

# Secondary region provider
provider "aws" {
  alias  = "secondary"
  region = "ap-southeast-1"
  
  default_tags {
    tags = {
      Environment = "lab"
      Project     = "MyLearning"
      ManagedBy   = "Terraform"
    }
  }
}

# Data sources
data "aws_availability_zones" "primary" {
  provider = aws.primary
  state    = "available"
}

data "aws_availability_zones" "secondary" {
  provider = aws.secondary
  state    = "available"
}

# Primary region VPC
resource "aws_vpc" "primary" {
  provider = aws.primary
  
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = {
    Name = "MyLearning-Primary-VPC"
  }
}

# Secondary region VPC
resource "aws_vpc" "secondary" {
  provider = aws.secondary
  
  cidr_block           = "10.1.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = {
    Name = "MyLearning-Secondary-VPC"
  }
}

# Internet Gateways
resource "aws_internet_gateway" "primary" {
  provider = aws.primary
  vpc_id   = aws_vpc.primary.id
  
  tags = {
    Name = "MyLearning-Primary-IGW"
  }
}

resource "aws_internet_gateway" "secondary" {
  provider = aws.secondary
  vpc_id   = aws_vpc.secondary.id
  
  tags = {
    Name = "MyLearning-Secondary-IGW"
  }
}

# Public subnets in primary region
resource "aws_subnet" "primary_public" {
  provider = aws.primary
  count    = 2
  
  vpc_id                  = aws_vpc.primary.id
  cidr_block              = "10.0.${count.index + 1}.0/24"
  availability_zone       = data.aws_availability_zones.primary.names[count.index]
  map_public_ip_on_launch = true
  
  tags = {
    Name = "MyLearning-Primary-Public-${count.index + 1}"
  }
}

# Public subnets in secondary region
resource "aws_subnet" "secondary_public" {
  provider = aws.secondary
  count    = 2
  
  vpc_id                  = aws_vpc.secondary.id
  cidr_block              = "10.1.${count.index + 1}.0/24"
  availability_zone       = data.aws_availability_zones.secondary.names[count.index]
  map_public_ip_on_launch = true
  
  tags = {
    Name = "MyLearning-Secondary-Public-${count.index + 1}"
  }
}

# S3 bucket for content (global)
resource "aws_s3_bucket" "content" {
  provider = aws.primary
  bucket   = "mylearning-lab-content-${random_id.bucket_suffix.hex}"
  
  tags = {
    Name = "MyLearning-Content-Bucket"
  }
}

resource "random_id" "bucket_suffix" {
  byte_length = 4
}

# S3 bucket versioning
resource "aws_s3_bucket_versioning" "content" {
  provider = aws.primary
  bucket   = aws_s3_bucket.content.id
  versioning_configuration {
    status = "Enabled"
  }
}

# S3 bucket encryption
resource "aws_s3_bucket_server_side_encryption_configuration" "content" {
  provider = aws.primary
  bucket   = aws_s3_bucket.content.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}
EOF

# Create outputs.tf
cat > outputs.tf << 'EOF'
output "primary_vpc_id" {
  description = "ID of the primary VPC"
  value       = aws_vpc.primary.id
}

output "secondary_vpc_id" {
  description = "ID of the secondary VPC"
  value       = aws_vpc.secondary.id
}

output "content_bucket_name" {
  description = "Name of the content S3 bucket"
  value       = aws_s3_bucket.content.bucket
}

output "primary_region" {
  description = "Primary AWS region"
  value       = "ap-south-1"
}

output "secondary_region" {
  description = "Secondary AWS region"
  value       = "ap-southeast-1"
}
EOF
```

#### Step 3: Deploy Infrastructure (30 minutes)
```bash
# Initialize Terraform
terraform init

# Validate configuration
terraform validate

# Plan deployment
terraform plan

# Apply configuration
terraform apply
# Type 'yes' when prompted

# Verify deployment
terraform show
```

#### Step 4: Test Multi-Region Setup (20 minutes)
```bash
# List VPCs in primary region
aws ec2 describe-vpcs --region ap-south-1 --output table

# List VPCs in secondary region
aws ec2 describe-vpcs --region ap-southeast-1 --output table

# List S3 buckets
aws s3 ls

# Upload test file to S3
echo "Multi-region test file" > test-multiregion.txt
aws s3 cp test-multiregion.txt s3://$(terraform output -raw content_bucket_name)/

# Verify file exists
aws s3 ls s3://$(terraform output -raw content_bucket_name)/
```

### Verification
- [ ] Terraform installed successfully
- [ ] Infrastructure deployed in both regions
- [ ] VPCs created in both regions
- [ ] S3 bucket created and accessible
- [ ] File uploaded successfully

## Lab 5: IAM Roles and Cross-Account Access

### Objective
Implement cross-account access patterns similar to MyLearning.com's multi-account strategy.

### Duration: 45 minutes

### Steps

#### Step 1: Create IAM Role for EC2 (15 minutes)
```bash
# Create trust policy for EC2
cat > ec2-trust-policy.json << 'EOF'
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
EOF

# Create the role
aws iam create-role \
    --role-name MyLearning-EC2-S3-Role \
    --assume-role-policy-document file://ec2-trust-policy.json

# Create permission policy for S3 access
cat > s3-access-policy.json << 'EOF'
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::mylearning-*/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::mylearning-*"
        }
    ]
}
EOF

# Create and attach the policy
aws iam create-policy \
    --policy-name MyLearning-S3-Access-Policy \
    --policy-document file://s3-access-policy.json

aws iam attach-role-policy \
    --role-name MyLearning-EC2-S3-Role \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/MyLearning-S3-Access-Policy

# Create instance profile
aws iam create-instance-profile \
    --instance-profile-name MyLearning-EC2-Profile

aws iam add-role-to-instance-profile \
    --instance-profile-name MyLearning-EC2-Profile \
    --role-name MyLearning-EC2-S3-Role
```

#### Step 2: Create Cross-Account Role (15 minutes)
```bash
# Create cross-account trust policy (replace TRUSTED-ACCOUNT-ID)
cat > cross-account-trust-policy.json << 'EOF'
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::TRUSTED-ACCOUNT-ID:root"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "sts:ExternalId": "MyLearning-Unique-ID-2021"
                },
                "Bool": {
                    "aws:MultiFactorAuthPresent": "true"
                }
            }
        }
    ]
}
EOF

# Create cross-account role
aws iam create-role \
    --role-name MyLearning-CrossAccount-ReadOnly \
    --assume-role-policy-document file://cross-account-trust-policy.json

# Attach read-only policy
aws iam attach-role-policy \
    --role-name MyLearning-CrossAccount-ReadOnly \
    --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
```

#### Step 3: Test Role Assumption (15 minutes)
```bash
# Test assuming the cross-account role (if you have MFA setup)
aws sts assume-role \
    --role-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):role/MyLearning-CrossAccount-ReadOnly \
    --role-session-name test-session \
    --external-id MyLearning-Unique-ID-2021 \
    --serial-number arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):mfa/your-mfa-device \
    --token-code 123456

# List all roles
aws iam list-roles --query 'Roles[?contains(RoleName, `MyLearning`)].RoleName'

# Get role details
aws iam get-role --role-name MyLearning-EC2-S3-Role
```

### Verification
- [ ] EC2 service role created successfully
- [ ] S3 access policy attached to role
- [ ] Instance profile created
- [ ] Cross-account role created with proper trust policy
- [ ] Roles visible in IAM console

## Lab 6: Monitoring and Cost Management

### Objective
Set up monitoring and cost management similar to MyLearning.com's approach.

### Duration: 30 minutes

### Steps

#### Step 1: Create CloudWatch Alarms (15 minutes)
```bash
# Create billing alarm
aws cloudwatch put-metric-alarm \
    --alarm-name "MyLearning-Billing-Alarm" \
    --alarm-description "Alarm when AWS charges exceed $10" \
    --metric-name EstimatedCharges \
    --namespace AWS/Billing \
    --statistic Maximum \
    --period 86400 \
    --threshold 10 \
    --comparison-operator GreaterThanThreshold \
    --dimensions Name=Currency,Value=USD \
    --evaluation-periods 1 \
    --alarm-actions arn:aws:sns:us-east-1:$(aws sts get-caller-identity --query Account --output text):billing-alerts

# Create EC2 CPU alarm (if you have EC2 instances)
aws cloudwatch put-metric-alarm \
    --alarm-name "MyLearning-High-CPU" \
    --alarm-description "Alarm when CPU exceeds 80%" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 80 \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 2
```

#### Step 2: Set Up Cost Budget (15 minutes)
```bash
# Create budget configuration
cat > budget-config.json << 'EOF'
{
    "BudgetName": "MyLearning-Monthly-Budget",
    "BudgetLimit": {
        "Amount": "50",
        "Unit": "USD"
    },
    "TimeUnit": "MONTHLY",
    "TimePeriod": {
        "Start": "2021-01-01T00:00:00Z",
        "End": "2087-06-15T00:00:00Z"
    },
    "BudgetType": "COST",
    "CostFilters": {
        "TagKey": ["Project"],
        "TagValue": ["MyLearning"]
    }
}
EOF

# Create notification configuration
cat > budget-notifications.json << 'EOF'
[
    {
        "Notification": {
            "NotificationType": "ACTUAL",
            "ComparisonOperator": "GREATER_THAN",
            "Threshold": 80,
            "ThresholdType": "PERCENTAGE"
        },
        "Subscribers": [
            {
                "SubscriptionType": "EMAIL",
                "Address": "your-email@domain.com"
            }
        ]
    },
    {
        "Notification": {
            "NotificationType": "FORECASTED",
            "ComparisonOperator": "GREATER_THAN",
            "Threshold": 100,
            "ThresholdType": "PERCENTAGE"
        },
        "Subscribers": [
            {
                "SubscriptionType": "EMAIL",
                "Address": "your-email@domain.com"
            }
        ]
    }
]
EOF

# Create the budget
aws budgets create-budget \
    --account-id $(aws sts get-caller-identity --query Account --output text) \
    --budget file://budget-config.json \
    --notifications-with-subscribers file://budget-notifications.json
```

### Verification
- [ ] CloudWatch alarms created
- [ ] Budget created with notifications
- [ ] Email notifications configured
- [ ] Alarms visible in CloudWatch console

## Lab Cleanup

### Cleanup Steps
```bash
# Destroy Terraform infrastructure
cd mylearning-infrastructure
terraform destroy
# Type 'yes' when prompted

# Delete S3 buckets (replace with your bucket names)
aws s3 rm s3://your-bucket-name --recursive
aws s3 rb s3://your-bucket-name

# Delete IAM resources
aws iam detach-role-policy \
    --role-name MyLearning-EC2-S3-Role \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/MyLearning-S3-Access-Policy

aws iam remove-role-from-instance-profile \
    --instance-profile-name MyLearning-EC2-Profile \
    --role-name MyLearning-EC2-S3-Role

aws iam delete-instance-profile --instance-profile-name MyLearning-EC2-Profile
aws iam delete-role --role-name MyLearning-EC2-S3-Role
aws iam delete-role --role-name MyLearning-CrossAccount-ReadOnly

aws iam delete-policy \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/MyLearning-S3-Access-Policy

aws iam delete-policy \
    --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/MyLearning-Developer-Policy

# Delete IAM users and groups
aws iam remove-user-from-group --group-name Developers --user-name dev-user-1
aws iam remove-user-from-group --group-name Developers --user-name dev-user-2
aws iam remove-user-from-group --group-name DevOps --user-name devops-user-1

aws iam delete-login-profile --user-name dev-user-1
aws iam delete-login-profile --user-name dev-user-2

aws iam delete-user --user-name dev-user-1
aws iam delete-user --user-name dev-user-2
aws iam delete-user --user-name devops-user-1

aws iam delete-group --group-name Developers
aws iam delete-group --group-name DevOps
aws iam delete-group --group-name ReadOnly

# Delete CloudWatch alarms
aws cloudwatch delete-alarms --alarm-names "MyLearning-Billing-Alarm" "MyLearning-High-CPU"

# Delete budget
aws budgets delete-budget \
    --account-id $(aws sts get-caller-identity --query Account --output text) \
    --budget-name "MyLearning-Monthly-Budget"
```

## Troubleshooting Guide

### Common Issues and Solutions

#### Issue 1: Access Denied Errors
```bash
# Check current identity
aws sts get-caller-identity

# Verify profile configuration
aws configure list --profile your-profile

# Check IAM permissions
aws iam get-user
aws iam list-attached-user-policies --user-name your-username
```

#### Issue 2: Terraform State Issues
```bash
# Refresh Terraform state
terraform refresh

# Import existing resources
terraform import aws_vpc.primary vpc-12345678

# Reset state if corrupted
terraform state list
terraform state rm resource.name
```

#### Issue 3: S3 Bucket Name Conflicts
```bash
# Generate unique bucket name
BUCKET_NAME="mylearning-lab-$(date +%s)-$(whoami)"
echo $BUCKET_NAME

# Check if bucket name is available
aws s3api head-bucket --bucket $BUCKET_NAME 2>/dev/null || echo "Bucket available"
```

#### Issue 4: Region-Specific Resources
```bash
# List available regions
aws ec2 describe-regions --query 'Regions[].RegionName' --output table

# Check service availability in region
aws ec2 describe-availability-zones --region ap-south-1

# Verify resource exists in correct region
aws ec2 describe-vpcs --region ap-south-1
```

## Next Steps

After completing these labs, you should:

1. **Practice regularly**: Set up a personal AWS account and practice these concepts
2. **Explore advanced features**: Try AWS Organizations, Control Tower, and Service Catalog
3. **Learn automation**: Dive deeper into Terraform, CloudFormation, and AWS CDK
4. **Study for certifications**: Consider AWS Certified Solutions Architect or Developer certifications
5. **Join communities**: Participate in AWS user groups and online forums

## Additional Resources

- [AWS Free Tier](https://aws.amazon.com/free/)
- [AWS CLI Documentation](https://docs.aws.amazon.com/cli/)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)

---

*These hands-on labs provide practical experience with the concepts covered in Module 2. Practice these exercises multiple times to build confidence and expertise.*