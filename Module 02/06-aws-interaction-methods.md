# AWS Interaction Methods

## MyLearning.com's Evolution: From Manual to Automated

### The Manual Era (March 2021)
When MyLearning.com first started with AWS, everything was done manually through the web console. Raj would spend hours clicking through the AWS Management Console to provision resources.

```
Manual Process Timeline:
├── Day 1: Learning the Console
│   ├── 4 hours to launch first EC2 instance
│   ├── 2 hours to configure security groups
│   ├── 3 hours to set up RDS database
│   └── 1 hour to create S3 bucket
├── Day 7: Repetitive Tasks
│   ├── 30 minutes daily to check resources
│   ├── 1 hour weekly for scaling adjustments
│   ├── 2 hours monthly for cost optimization
│   └── 4 hours for environment replication
├── Day 30: Scaling Problems
│   ├── Cannot keep up with growth
│   ├── Human errors in configuration
│   ├── Inconsistent environments
│   └── No version control for changes
└── Day 60: The Breaking Point
    ├── 8 hours to replicate production
    ├── Configuration drift between environments
    ├── No audit trail of changes
    └── Team productivity bottleneck
```

### The Automation Journey (June 2021 - Present)
The team realized they needed to automate their AWS interactions to scale effectively.

```
Automation Evolution:
├── Phase 1: AWS CLI Scripts
│   ├── Basic resource provisioning
│   ├── Backup automation
│   ├── Simple monitoring scripts
│   └── Cost reporting automation
├── Phase 2: Infrastructure as Code
│   ├── Terraform for multi-cloud
│   ├── CloudFormation for AWS-native
│   ├── Version-controlled infrastructure
│   └── Environment consistency
├── Phase 3: Advanced Automation
│   ├── CI/CD pipeline integration
│   ├── GitOps workflows
│   ├── Policy as Code
│   └── Compliance automation
└── Phase 4: AI-Assisted Operations
    ├── Predictive scaling
    ├── Automated optimization
    ├── Intelligent monitoring
    └── Self-healing infrastructure
```

## 1. AWS Management Console

### Overview
The AWS Management Console is a web-based user interface for accessing and managing AWS services.

### MyLearning.com's Console Usage Strategy
```
Console Usage Patterns:
├── Learning and Exploration (20%)
│   ├── New service discovery
│   ├── Feature exploration
│   ├── Quick prototyping
│   └── Training new team members
├── Monitoring and Troubleshooting (40%)
│   ├── Real-time monitoring dashboards
│   ├── Log analysis and debugging
│   ├── Performance investigation
│   └── Incident response
├── One-time Configuration (25%)
│   ├── Initial service setup
│   ├── Complex configuration wizards
│   ├── Security policy creation
│   └── Compliance configuration
├── Emergency Operations (10%)
│   ├── Incident response
│   ├── Manual failover
│   ├── Emergency scaling
│   └── Critical fixes
└── Administrative Tasks (5%)
    ├── User management
    ├── Billing review
    ├── Support case management
    └── Account settings
```

### Console Best Practices
```
Console Security and Efficiency:
├── Security Measures
│   ├── Always use MFA
│   ├── Use IAM users, not root
│   ├── Regular session timeouts
│   ├── IP-based access restrictions
│   └── Activity logging via CloudTrail
├── Efficiency Tips
│   ├── Bookmark frequently used services
│   ├── Use service search functionality
│   ├── Customize dashboard widgets
│   ├── Use resource groups for organization
│   └── Set up billing alerts
├── Team Collaboration
│   ├── Shared resource tagging strategy
│   ├── Consistent naming conventions
│   ├── Documentation of manual changes
│   ├── Change approval processes
│   └── Knowledge sharing sessions
└── Limitations Awareness
    ├── Not suitable for automation
    ├── No version control
    ├── Human error prone
    ├── Time-consuming for repetitive tasks
    └── Limited bulk operations
```

### MyLearning.com's Console Customization
```
Customized Console Experience:
├── Dashboard Widgets
│   ├── EC2 instance status
│   ├── RDS performance metrics
│   ├── S3 bucket usage
│   ├── Cost and billing alerts
│   └── CloudWatch alarms
├── Favorite Services
│   ├── EC2 (compute resources)
│   ├── RDS (database management)
│   ├── S3 (storage)
│   ├── CloudWatch (monitoring)
│   ├── IAM (security)
│   └── Cost Explorer (billing)
├── Resource Groups
│   ├── Production resources
│   ├── Staging resources
│   ├── Development resources
│   ├── Shared services
│   └── Cost center groupings
└── Shortcuts and Bookmarks
    ├── Frequently accessed dashboards
    ├── Common configuration pages
    ├── Monitoring and alerting
    └── Billing and cost management
```

## 2. AWS Command Line Interface (CLI)

### Overview
The AWS CLI is a unified tool to manage AWS services from the command line and automate them through scripts.

### MyLearning.com's CLI Journey
```
CLI Adoption Timeline:
├── Month 1: Basic Commands
│   ├── aws ec2 describe-instances
│   ├── aws s3 ls
│   ├── aws rds describe-db-instances
│   └── aws iam list-users
├── Month 2: Automation Scripts
│   ├── Daily backup scripts
│   ├── Resource monitoring scripts
│   ├── Cost reporting automation
│   └── Security audit scripts
├── Month 3: Advanced Usage
│   ├── Complex queries with --query
│   ├── Output formatting with --output
│   ├── Profile management for multiple accounts
│   └── Integration with CI/CD pipelines
└── Month 6: Expert Level
    ├── Custom CLI extensions
    ├── Advanced scripting patterns
    ├── Error handling and retry logic
    └── Performance optimization
```

### CLI Installation and Configuration
```bash
# Installation (macOS)
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /

# Installation (Linux)
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Configuration
aws configure
# AWS Access Key ID: AKIAIOSFODNN7EXAMPLE
# AWS Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
# Default region name: ap-south-1
# Default output format: json
```

### MyLearning.com's CLI Configuration
```bash
# Multiple profile setup
aws configure --profile mylearning-prod
aws configure --profile mylearning-staging
aws configure --profile mylearning-dev

# Profile configuration file (~/.aws/config)
[default]
region = ap-south-1
output = json

[profile mylearning-prod]
region = ap-south-1
output = table
role_arn = arn:aws:iam::222222222222:role/ProductionAccess
source_profile = default
mfa_serial = arn:aws:iam::111111111111:mfa/raj.kumar

[profile mylearning-staging]
region = ap-south-1
output = json
role_arn = arn:aws:iam::333333333333:role/StagingAccess
source_profile = default

[profile mylearning-dev]
region = ap-south-1
output = yaml
role_arn = arn:aws:iam::444444444444:role/DevelopmentAccess
source_profile = default
```

### Common CLI Commands Used by MyLearning.com
```bash
# EC2 Management
aws ec2 describe-instances --profile mylearning-prod
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 create-tags --resources i-1234567890abcdef0 --tags Key=Environment,Value=Production

# S3 Operations
aws s3 ls s3://mylearning-content/
aws s3 sync ./local-folder s3://mylearning-content/courses/
aws s3 cp s3://mylearning-backups/db-backup.sql ./
aws s3api put-bucket-policy --bucket mylearning-content --policy file://bucket-policy.json

# RDS Management
aws rds describe-db-instances --profile mylearning-prod
aws rds create-db-snapshot --db-instance-identifier mylearning-prod-db --db-snapshot-identifier backup-$(date +%Y%m%d)
aws rds modify-db-instance --db-instance-identifier mylearning-prod-db --allocated-storage 200

# IAM Operations
aws iam list-users --profile mylearning-prod
aws iam create-user --user-name new-developer
aws iam attach-user-policy --user-name new-developer --policy-arn arn:aws:iam::aws:policy/PowerUserAccess
aws iam create-access-key --user-name new-developer

# CloudWatch Monitoring
aws cloudwatch get-metric-statistics --namespace AWS/EC2 --metric-name CPUUtilization --dimensions Name=InstanceId,Value=i-1234567890abcdef0 --statistics Average --start-time 2021-01-01T00:00:00Z --end-time 2021-01-02T00:00:00Z --period 3600

# Cost and Billing
aws ce get-cost-and-usage --time-period Start=2021-01-01,End=2021-01-31 --granularity MONTHLY --metrics BlendedCost --group-by Type=DIMENSION,Key=SERVICE
```

### MyLearning.com's Automation Scripts
```bash
#!/bin/bash
# Daily backup script for MyLearning.com

# Set variables
DATE=$(date +%Y%m%d)
PROFILE="mylearning-prod"
DB_INSTANCE="mylearning-prod-db"
S3_BUCKET="mylearning-backups"

# Create RDS snapshot
echo "Creating RDS snapshot..."
aws rds create-db-snapshot \
    --profile $PROFILE \
    --db-instance-identifier $DB_INSTANCE \
    --db-snapshot-identifier "daily-backup-$DATE"

# Backup application files
echo "Backing up application files..."
aws s3 sync /var/www/mylearning s3://$S3_BUCKET/app-backup-$DATE/ \
    --profile $PROFILE \
    --exclude "*.log" \
    --exclude "tmp/*"

# Create EC2 AMI
echo "Creating EC2 AMI..."
INSTANCE_ID=$(aws ec2 describe-instances \
    --profile $PROFILE \
    --filters "Name=tag:Name,Values=MyLearning-Prod-Web" \
    --query "Reservations[0].Instances[0].InstanceId" \
    --output text)

aws ec2 create-image \
    --profile $PROFILE \
    --instance-id $INSTANCE_ID \
    --name "MyLearning-Prod-$DATE" \
    --description "Daily backup of production web server"

echo "Backup completed successfully!"
```

## 3. AWS Software Development Kits (SDKs)

### Overview
AWS SDKs provide APIs for AWS services in various programming languages, enabling developers to integrate AWS services into their applications.

### MyLearning.com's SDK Usage
```
SDK Implementation by Service:
├── Python (Boto3) - 60%
│   ├── Backend API development
│   ├── Data processing scripts
│   ├── Machine learning pipelines
│   └── Administrative automation
├── Node.js (AWS SDK) - 25%
│   ├── Frontend API integration
│   ├── Lambda functions
│   ├── Real-time applications
│   └── Serverless backends
├── Java (AWS SDK) - 10%
│   ├── Enterprise integrations
│   ├── High-performance applications
│   ├── Legacy system integration
│   └── Batch processing jobs
└── Go (AWS SDK) - 5%
    ├── Microservices development
    ├── CLI tool development
    ├── Performance-critical applications
    └── Container-based services
```

### Python (Boto3) Examples
```python
import boto3
from botocore.exceptions import ClientError
import json
from datetime import datetime

# MyLearning.com's AWS utility class
class MyLearningAWS:
    def __init__(self, profile_name='default'):
        self.session = boto3.Session(profile_name=profile_name)
        self.ec2 = self.session.client('ec2')
        self.s3 = self.session.client('s3')
        self.rds = self.session.client('rds')
        self.cloudwatch = self.session.client('cloudwatch')
    
    def get_instance_status(self, tag_name, tag_value):
        """Get status of EC2 instances by tag"""
        try:
            response = self.ec2.describe_instances(
                Filters=[
                    {'Name': f'tag:{tag_name}', 'Values': [tag_value]},
                    {'Name': 'instance-state-name', 'Values': ['running', 'stopped']}
                ]
            )
            
            instances = []
            for reservation in response['Reservations']:
                for instance in reservation['Instances']:
                    instances.append({
                        'InstanceId': instance['InstanceId'],
                        'State': instance['State']['Name'],
                        'InstanceType': instance['InstanceType'],
                        'LaunchTime': instance['LaunchTime']
                    })
            return instances
        except ClientError as e:
            print(f"Error getting instance status: {e}")
            return []
    
    def upload_course_content(self, local_file, s3_key):
        """Upload course content to S3 with proper metadata"""
        try:
            self.s3.upload_file(
                local_file, 
                'mylearning-content', 
                s3_key,
                ExtraArgs={
                    'ContentType': 'application/pdf',
                    'Metadata': {
                        'uploaded-by': 'mylearning-app',
                        'upload-date': datetime.now().isoformat()
                    },
                    'ServerSideEncryption': 'AES256'
                }
            )
            print(f"Successfully uploaded {local_file} to s3://mylearning-content/{s3_key}")
            return True
        except ClientError as e:
            print(f"Error uploading file: {e}")
            return False
    
    def create_db_snapshot(self, db_instance_id):
        """Create RDS snapshot with timestamp"""
        snapshot_id = f"{db_instance_id}-{datetime.now().strftime('%Y%m%d-%H%M%S')}"
        try:
            response = self.rds.create_db_snapshot(
                DBSnapshotIdentifier=snapshot_id,
                DBInstanceIdentifier=db_instance_id
            )
            print(f"Snapshot creation initiated: {snapshot_id}")
            return response['DBSnapshot']['DBSnapshotIdentifier']
        except ClientError as e:
            print(f"Error creating snapshot: {e}")
            return None
    
    def get_cost_metrics(self, start_date, end_date):
        """Get cost metrics for the specified period"""
        try:
            ce_client = self.session.client('ce')
            response = ce_client.get_cost_and_usage(
                TimePeriod={
                    'Start': start_date,
                    'End': end_date
                },
                Granularity='DAILY',
                Metrics=['BlendedCost'],
                GroupBy=[
                    {'Type': 'DIMENSION', 'Key': 'SERVICE'}
                ]
            )
            return response['ResultsByTime']
        except ClientError as e:
            print(f"Error getting cost metrics: {e}")
            return []

# Usage example
if __name__ == "__main__":
    # Initialize AWS client
    aws_client = MyLearningAWS(profile_name='mylearning-prod')
    
    # Get production instance status
    prod_instances = aws_client.get_instance_status('Environment', 'Production')
    print(f"Found {len(prod_instances)} production instances")
    
    # Upload course content
    aws_client.upload_course_content(
        'course-materials/python-basics.pdf',
        'courses/programming/python-basics.pdf'
    )
    
    # Create database backup
    snapshot_id = aws_client.create_db_snapshot('mylearning-prod-db')
    if snapshot_id:
        print(f"Backup initiated with ID: {snapshot_id}")
```

### Node.js SDK Examples
```javascript
// MyLearning.com's Node.js AWS utilities
const AWS = require('aws-sdk');

// Configure AWS SDK
AWS.config.update({
    region: 'ap-south-1',
    profile: 'mylearning-prod'
});

class MyLearningAWS {
    constructor() {
        this.s3 = new AWS.S3();
        this.lambda = new AWS.Lambda();
        this.dynamodb = new AWS.DynamoDB.DocumentClient();
        this.sns = new AWS.SNS();
    }

    // Upload user-generated content
    async uploadUserContent(userId, file, contentType) {
        const key = `user-content/${userId}/${Date.now()}-${file.originalname}`;
        
        const params = {
            Bucket: 'mylearning-user-content',
            Key: key,
            Body: file.buffer,
            ContentType: contentType,
            Metadata: {
                'user-id': userId,
                'upload-timestamp': new Date().toISOString()
            },
            ServerSideEncryption: 'AES256'
        };

        try {
            const result = await this.s3.upload(params).promise();
            console.log(`File uploaded successfully: ${result.Location}`);
            return result.Location;
        } catch (error) {
            console.error('Error uploading file:', error);
            throw error;
        }
    }

    // Process video content using Lambda
    async processVideo(videoKey) {
        const params = {
            FunctionName: 'mylearning-video-processor',
            Payload: JSON.stringify({
                videoKey: videoKey,
                outputBucket: 'mylearning-processed-videos',
                resolutions: ['720p', '1080p'],
                formats: ['mp4', 'webm']
            })
        };

        try {
            const result = await this.lambda.invoke(params).promise();
            const response = JSON.parse(result.Payload);
            console.log('Video processing initiated:', response);
            return response;
        } catch (error) {
            console.error('Error processing video:', error);
            throw error;
        }
    }

    // Store user progress in DynamoDB
    async updateUserProgress(userId, courseId, lessonId, progress) {
        const params = {
            TableName: 'UserProgress',
            Key: {
                userId: userId,
                courseId: courseId
            },
            UpdateExpression: 'SET #lesson = :progress, lastUpdated = :timestamp',
            ExpressionAttributeNames: {
                '#lesson': lessonId
            },
            ExpressionAttributeValues: {
                ':progress': progress,
                ':timestamp': new Date().toISOString()
            }
        };

        try {
            await this.dynamodb.update(params).promise();
            console.log(`Progress updated for user ${userId}, course ${courseId}`);
        } catch (error) {
            console.error('Error updating progress:', error);
            throw error;
        }
    }

    // Send notification to users
    async sendNotification(topicArn, message, subject) {
        const params = {
            TopicArn: topicArn,
            Message: JSON.stringify(message),
            Subject: subject,
            MessageAttributes: {
                'notification-type': {
                    DataType: 'String',
                    StringValue: 'course-update'
                }
            }
        };

        try {
            const result = await this.sns.publish(params).promise();
            console.log('Notification sent:', result.MessageId);
            return result.MessageId;
        } catch (error) {
            console.error('Error sending notification:', error);
            throw error;
        }
    }
}

// Express.js integration example
const express = require('express');
const multer = require('multer');
const app = express();
const awsClient = new MyLearningAWS();
const upload = multer();

// Upload endpoint
app.post('/api/upload', upload.single('file'), async (req, res) => {
    try {
        const userId = req.user.id;
        const file = req.file;
        
        const fileUrl = await awsClient.uploadUserContent(
            userId, 
            file, 
            file.mimetype
        );
        
        res.json({ success: true, fileUrl: fileUrl });
    } catch (error) {
        res.status(500).json({ success: false, error: error.message });
    }
});

// Progress update endpoint
app.post('/api/progress', async (req, res) => {
    try {
        const { userId, courseId, lessonId, progress } = req.body;
        
        await awsClient.updateUserProgress(userId, courseId, lessonId, progress);
        
        res.json({ success: true });
    } catch (error) {
        res.status(500).json({ success: false, error: error.message });
    }
});

module.exports = MyLearningAWS;
```

## 4. Infrastructure as Code (IaC)

### Overview
Infrastructure as Code allows MyLearning.com to manage and provision their AWS infrastructure through code rather than manual processes.

### MyLearning.com's IaC Strategy
```
IaC Tool Selection:
├── Terraform (Primary - 70%)
│   ├── Multi-cloud capability
│   ├── Strong community support
│   ├── Mature ecosystem
│   └── Version control friendly
├── AWS CloudFormation (Secondary - 25%)
│   ├── Native AWS integration
│   ├── AWS service coverage
│   ├── Stack management
│   └── Change sets
├── AWS CDK (Emerging - 5%)
│   ├── Developer-friendly
│   ├── Programming language support
│   ├── High-level constructs
│   └── Type safety
└── Pulumi (Experimental - <1%)
    ├── Modern approach
    ├── Programming language native
    ├── State management
    └── Cloud-native
```

### Terraform Implementation
```hcl
# MyLearning.com's main Terraform configuration
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  
  backend "s3" {
    bucket         = "mylearning-terraform-state"
    key            = "production/terraform.tfstate"
    region         = "ap-south-1"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
  }
}

# Provider configuration
provider "aws" {
  region  = var.aws_region
  profile = var.aws_profile
  
  default_tags {
    tags = {
      Environment   = var.environment
      Project       = "MyLearning"
      ManagedBy     = "Terraform"
      CostCenter    = "Engineering"
      Owner         = "DevOps Team"
    }
  }
}

# Variables
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "ap-south-1"
}

variable "environment" {
  description = "Environment name"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.medium"
}

# Data sources
data "aws_availability_zones" "available" {
  state = "available"
}

data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  
  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

# VPC Configuration
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = {
    Name = "MyLearning-${var.environment}-VPC"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
  
  tags = {
    Name = "MyLearning-${var.environment}-IGW"
  }
}

# Public Subnets
resource "aws_subnet" "public" {
  count = 2
  
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.${count.index + 1}.0/24"
  availability_zone       = data.aws_availability_zones.available.names[count.index]
  map_public_ip_on_launch = true
  
  tags = {
    Name = "MyLearning-${var.environment}-Public-${count.index + 1}"
    Type = "Public"
  }
}

# Private Subnets
resource "aws_subnet" "private" {
  count = 2
  
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.${count.index + 10}.0/24"
  availability_zone = data.aws_availability_zones.available.names[count.index]
  
  tags = {
    Name = "MyLearning-${var.environment}-Private-${count.index + 1}"
    Type = "Private"
  }
}

# Route Tables
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }
  
  tags = {
    Name = "MyLearning-${var.environment}-Public-RT"
  }
}

resource "aws_route_table_association" "public" {
  count = length(aws_subnet.public)
  
  subnet_id      = aws_subnet.public[count.index].id
  route_table_id = aws_route_table.public.id
}

# Security Groups
resource "aws_security_group" "web" {
  name_prefix = "mylearning-${var.environment}-web-"
  vpc_id      = aws_vpc.main.id
  
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["10.0.0.0/16"]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = {
    Name = "MyLearning-${var.environment}-Web-SG"
  }
}

# Application Load Balancer
resource "aws_lb" "main" {
  name               = "mylearning-${var.environment}-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.web.id]
  subnets            = aws_subnet.public[*].id
  
  enable_deletion_protection = var.environment == "prod" ? true : false
  
  tags = {
    Name = "MyLearning-${var.environment}-ALB"
  }
}

# Launch Template
resource "aws_launch_template" "web" {
  name_prefix   = "mylearning-${var.environment}-web-"
  image_id      = data.aws_ami.amazon_linux.id
  instance_type = var.instance_type
  
  vpc_security_group_ids = [aws_security_group.web.id]
  
  user_data = base64encode(templatefile("${path.module}/user_data.sh", {
    environment = var.environment
  }))
  
  tag_specifications {
    resource_type = "instance"
    tags = {
      Name = "MyLearning-${var.environment}-Web"
    }
  }
}

# Auto Scaling Group
resource "aws_autoscaling_group" "web" {
  name                = "mylearning-${var.environment}-asg"
  vpc_zone_identifier = aws_subnet.private[*].id
  target_group_arns   = [aws_lb_target_group.web.arn]
  health_check_type   = "ELB"
  
  min_size         = var.environment == "prod" ? 2 : 1
  max_size         = var.environment == "prod" ? 10 : 3
  desired_capacity = var.environment == "prod" ? 3 : 1
  
  launch_template {
    id      = aws_launch_template.web.id
    version = "$Latest"
  }
  
  tag {
    key                 = "Name"
    value               = "MyLearning-${var.environment}-ASG"
    propagate_at_launch = false
  }
}

# RDS Subnet Group
resource "aws_db_subnet_group" "main" {
  name       = "mylearning-${var.environment}-db-subnet-group"
  subnet_ids = aws_subnet.private[*].id
  
  tags = {
    Name = "MyLearning-${var.environment}-DB-SubnetGroup"
  }
}

# RDS Instance
resource "aws_db_instance" "main" {
  identifier = "mylearning-${var.environment}-db"
  
  engine         = "mysql"
  engine_version = "8.0"
  instance_class = var.environment == "prod" ? "db.t3.medium" : "db.t3.micro"
  
  allocated_storage     = var.environment == "prod" ? 100 : 20
  max_allocated_storage = var.environment == "prod" ? 1000 : 100
  storage_encrypted     = true
  
  db_name  = "mylearning"
  username = "admin"
  password = random_password.db_password.result
  
  vpc_security_group_ids = [aws_security_group.database.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name
  
  backup_retention_period = var.environment == "prod" ? 7 : 1
  backup_window          = "03:00-04:00"
  maintenance_window     = "sun:04:00-sun:05:00"
  
  skip_final_snapshot = var.environment != "prod"
  
  tags = {
    Name = "MyLearning-${var.environment}-DB"
  }
}

# Random password for database
resource "random_password" "db_password" {
  length  = 16
  special = true
}

# Store database password in AWS Secrets Manager
resource "aws_secretsmanager_secret" "db_password" {
  name = "mylearning-${var.environment}-db-password"
}

resource "aws_secretsmanager_secret_version" "db_password" {
  secret_id     = aws_secretsmanager_secret.db_password.id
  secret_string = jsonencode({
    username = aws_db_instance.main.username
    password = random_password.db_password.result
    endpoint = aws_db_instance.main.endpoint
    port     = aws_db_instance.main.port
  })
}

# Outputs
output "load_balancer_dns" {
  description = "DNS name of the load balancer"
  value       = aws_lb.main.dns_name
}

output "database_endpoint" {
  description = "RDS instance endpoint"
  value       = aws_db_instance.main.endpoint
  sensitive   = true
}

output "vpc_id" {
  description = "ID of the VPC"
  value       = aws_vpc.main.id
}
```

### CloudFormation Template Example
```yaml
# MyLearning.com CloudFormation template for S3 and CloudFront
AWSTemplateFormatVersion: '2010-09-09'
Description: 'MyLearning.com Content Delivery Infrastructure'

Parameters:
  Environment:
    Type: String
    Default: prod
    AllowedValues: [dev, staging, prod]
    Description: Environment name
  
  DomainName:
    Type: String
    Default: content.mylearning.com
    Description: Domain name for CloudFront distribution

Resources:
  # S3 Bucket for content storage
  ContentBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub 'mylearning-${Environment}-content'
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: AES256
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
      VersioningConfiguration:
        Status: Enabled
      LifecycleConfiguration:
        Rules:
          - Id: DeleteOldVersions
            Status: Enabled
            NoncurrentVersionExpirationInDays: 30
      Tags:
        - Key: Environment
          Value: !Ref Environment
        - Key: Project
          Value: MyLearning

  # S3 Bucket Policy
  ContentBucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref ContentBucket
      PolicyDocument:
        Statement:
          - Sid: AllowCloudFrontAccess
            Effect: Allow
            Principal:
              AWS: !Sub 'arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity ${OriginAccessIdentity}'
            Action: 's3:GetObject'
            Resource: !Sub '${ContentBucket}/*'
          - Sid: DenyInsecureConnections
            Effect: Deny
            Principal: '*'
            Action: 's3:*'
            Resource:
              - !Sub '${ContentBucket}/*'
              - !Ref ContentBucket
            Condition:
              Bool:
                'aws:SecureTransport': 'false'

  # CloudFront Origin Access Identity
  OriginAccessIdentity:
    Type: AWS::CloudFront::OriginAccessIdentity
    Properties:
      OriginAccessIdentityConfig:
        Comment: !Sub 'OAI for MyLearning ${Environment} content'

  # CloudFront Distribution
  ContentDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Aliases:
          - !Ref DomainName
        Origins:
          - Id: S3Origin
            DomainName: !GetAtt ContentBucket.RegionalDomainName
            S3OriginConfig:
              OriginAccessIdentity: !Sub 'origin-access-identity/cloudfront/${OriginAccessIdentity}'
        DefaultCacheBehavior:
          TargetOriginId: S3Origin
          ViewerProtocolPolicy: redirect-to-https
          AllowedMethods: [GET, HEAD, OPTIONS]
          CachedMethods: [GET, HEAD, OPTIONS]
          ForwardedValues:
            QueryString: false
            Cookies:
              Forward: none
          MinTTL: 0
          DefaultTTL: 86400
          MaxTTL: 31536000
          Compress: true
        CacheBehaviors:
          - PathPattern: '/api/*'
            TargetOriginId: S3Origin
            ViewerProtocolPolicy: https-only
            AllowedMethods: [GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE]
            CachedMethods: [GET, HEAD, OPTIONS]
            ForwardedValues:
              QueryString: true
              Headers: [Authorization, Content-Type]
            MinTTL: 0
            DefaultTTL: 0
            MaxTTL: 0
        Enabled: true
        Comment: !Sub 'MyLearning ${Environment} Content Distribution'
        DefaultRootObject: index.html
        PriceClass: PriceClass_All
        ViewerCertificate:
          AcmCertificateArn: !Ref SSLCertificate
          SslSupportMethod: sni-only
          MinimumProtocolVersion: TLSv1.2_2021
      Tags:
        - Key: Environment
          Value: !Ref Environment
        - Key: Project
          Value: MyLearning

  # SSL Certificate
  SSLCertificate:
    Type: AWS::CertificateManager::Certificate
    Properties:
      DomainName: !Ref DomainName
      ValidationMethod: DNS
      Tags:
        - Key: Environment
          Value: !Ref Environment
        - Key: Project
          Value: MyLearning

Outputs:
  ContentBucketName:
    Description: Name of the S3 content bucket
    Value: !Ref ContentBucket
    Export:
      Name: !Sub '${AWS::StackName}-ContentBucket'
  
  DistributionDomainName:
    Description: CloudFront distribution domain name
    Value: !GetAtt ContentDistribution.DomainName
    Export:
      Name: !Sub '${AWS::StackName}-DistributionDomain'
  
  DistributionId:
    Description: CloudFront distribution ID
    Value: !Ref ContentDistribution
    Export:
      Name: !Sub '${AWS::StackName}-DistributionId'
```

---

*Next: Hands-on labs and exercises to practice the concepts learned, followed by best practices and security recommendations.*