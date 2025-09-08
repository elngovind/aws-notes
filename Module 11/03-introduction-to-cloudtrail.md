# Introduction to AWS CloudTrail

## MyLearning.com's Audit and Compliance Journey

### The Compliance Wake-Up Call
Six months into their AWS journey, MyLearning.com received an unexpected email from their enterprise client, TechCorp Industries:

```
From: compliance@techcorp.com
To: admin@mylearning.com
Subject: Security Audit Requirements - Urgent

Dear MyLearning.com Team,

As part of our vendor security assessment, we require detailed audit logs 
of all administrative activities in your AWS environment. Specifically:

1. Who accessed what resources and when?
2. What configuration changes were made?
3. Who has administrative privileges?
4. Evidence of data access patterns
5. Proof of security best practices

Please provide this information within 2 weeks or we may need to 
reconsider our partnership.

Best regards,
TechCorp Compliance Team
```

This email sent shockwaves through the MyLearning.com team. They realized they had been so focused on building features that they had overlooked crucial audit and compliance requirements.

### The Audit Challenge
```
MyLearning.com's Audit Gaps:
├── Missing Activity Logs
│   ├── No record of who created/modified resources
│   ├── Unknown API call history
│   ├── Missing login/logout tracking
│   └── No change management audit trail
├── Compliance Requirements
│   ├── SOC 2 Type II preparation needed
│   ├── GDPR data access logging
│   ├── PCI DSS audit trails
│   └── ISO 27001 compliance evidence
├── Security Concerns
│   ├── Potential unauthorized access undetected
│   ├── No forensic investigation capability
│   ├── Insider threat detection gaps
│   └── Incident response limitations
└── Business Impact
    ├── Risk of losing enterprise clients
    ├── Potential regulatory fines
    ├── Reputation damage
    └── Competitive disadvantage
```

## What is AWS CloudTrail?

### CloudTrail Overview
AWS CloudTrail is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account. It records AWS API calls and delivers log files to an Amazon S3 bucket.

```
CloudTrail Core Functions:
├── API Call Logging
│   ├── Records all AWS API calls
│   ├── Captures caller identity
│   ├── Logs request parameters
│   ├── Records response elements
│   └── Timestamps all activities
├── Event Types
│   ├── Management events (control plane)
│   ├── Data events (data plane)
│   ├── Insight events (unusual patterns)
│   └── Configuration changes
├── Delivery Options
│   ├── S3 bucket storage
│   ├── CloudWatch Logs integration
│   ├── Real-time event processing
│   └── Multi-region aggregation
└── Analysis Capabilities
    ├── Event history search
    ├── CloudWatch integration
    ├── Third-party SIEM tools
    └── Custom analytics
```

### CloudTrail Benefits for MyLearning.com
```
Immediate Value Proposition:
├── Compliance Readiness
│   ├── Complete audit trail for all AWS activities
│   ├── Evidence for compliance frameworks
│   ├── Automated log retention policies
│   └── Tamper-evident log integrity
├── Security Enhancement
│   ├── Unauthorized access detection
│   ├── Privilege escalation monitoring
│   ├── Unusual activity alerts
│   └── Forensic investigation support
├── Operational Insights
│   ├── Resource usage patterns
│   ├── Cost optimization opportunities
│   ├── Performance troubleshooting
│   └── Change impact analysis
└── Risk Management
    ├── Proactive threat detection
    ├── Incident response acceleration
    ├── Compliance violation prevention
    └── Business continuity assurance
```

## CloudTrail Event Types

### Management Events
Management events provide information about management operations performed on resources in your AWS account.

```
Management Event Examples:
├── IAM Operations
│   ├── CreateUser, DeleteUser
│   ├── AttachUserPolicy, DetachUserPolicy
│   ├── CreateRole, AssumeRole
│   └── PutRolePolicy, DeleteRolePolicy
├── EC2 Operations
│   ├── RunInstances, TerminateInstances
│   ├── CreateSecurityGroup, AuthorizeSecurityGroupIngress
│   ├── CreateSnapshot, DeleteSnapshot
│   └── ModifyInstanceAttribute
├── S3 Operations
│   ├── CreateBucket, DeleteBucket
│   ├── PutBucketPolicy, DeleteBucketPolicy
│   ├── PutBucketVersioning
│   └── PutBucketEncryption
├── RDS Operations
│   ├── CreateDBInstance, DeleteDBInstance
│   ├── ModifyDBInstance
│   ├── CreateDBSnapshot
│   └── RestoreDBInstanceFromDBSnapshot
└── VPC Operations
    ├── CreateVpc, DeleteVpc
    ├── CreateSubnet, DeleteSubnet
    ├── CreateRouteTable, DeleteRouteTable
    └── CreateInternetGateway, AttachInternetGateway
```

### Data Events
Data events provide information about resource operations performed on or within a resource.

```
Data Event Examples:
├── S3 Object Operations
│   ├── GetObject (object downloads)
│   ├── PutObject (object uploads)
│   ├── DeleteObject (object deletions)
│   └── RestoreObject (glacier restores)
├── Lambda Function Operations
│   ├── Invoke (function executions)
│   └── GetFunction (function code access)
├── DynamoDB Operations
│   ├── GetItem, PutItem
│   ├── UpdateItem, DeleteItem
│   ├── Query, Scan
│   └── BatchGetItem, BatchWriteItem
└── EBS Operations
    ├── CreateSnapshot
    ├── DeleteSnapshot
    └── ModifySnapshotAttribute
```

### Insight Events
CloudTrail Insights helps identify unusual operational activity in your AWS account.

```
Insight Event Triggers:
├── Unusual API Call Patterns
│   ├── Sudden spike in API calls
│   ├── Unusual error rates
│   ├── New API calls from unfamiliar sources
│   └── Abnormal geographic access patterns
├── Resource Creation Anomalies
│   ├── Rapid resource provisioning
│   ├── Unusual instance types launched
│   ├── Unexpected storage consumption
│   └── Abnormal network traffic patterns
├── Permission Changes
│   ├── Bulk permission modifications
│   ├── Privilege escalation attempts
│   ├── Unusual role assumptions
│   └── Policy attachment spikes
└── Cost Impact Events
    ├── Expensive resource launches
    ├── High-cost API operations
    ├── Unusual data transfer patterns
    └── Resource scaling anomalies
```

## Setting Up CloudTrail for MyLearning.com

### Basic CloudTrail Configuration

#### Creating the First Trail
```python
import boto3
import json
from datetime import datetime

class MyLearningCloudTrail:
    def __init__(self):
        self.cloudtrail = boto3.client('cloudtrail')
        self.s3 = boto3.client('s3')
        self.iam = boto3.client('iam')
    
    def create_cloudtrail_bucket(self):
        """Create S3 bucket for CloudTrail logs"""
        
        bucket_name = 'mylearning-cloudtrail-logs-ap-south-1'
        
        # Create S3 bucket
        self.s3.create_bucket(
            Bucket=bucket_name,
            CreateBucketConfiguration={'LocationConstraint': 'ap-south-1'}
        )
        
        # Enable versioning
        self.s3.put_bucket_versioning(
            Bucket=bucket_name,
            VersioningConfiguration={'Status': 'Enabled'}
        )
        
        # Configure bucket policy for CloudTrail
        bucket_policy = {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "AWSCloudTrailAclCheck",
                    "Effect": "Allow",
                    "Principal": {"Service": "cloudtrail.amazonaws.com"},
                    "Action": "s3:GetBucketAcl",
                    "Resource": f"arn:aws:s3:::{bucket_name}"
                },
                {
                    "Sid": "AWSCloudTrailWrite",
                    "Effect": "Allow",
                    "Principal": {"Service": "cloudtrail.amazonaws.com"},
                    "Action": "s3:PutObject",
                    "Resource": f"arn:aws:s3:::{bucket_name}/*",
                    "Condition": {
                        "StringEquals": {
                            "s3:x-amz-acl": "bucket-owner-full-control"
                        }
                    }
                }
            ]
        }
        
        self.s3.put_bucket_policy(
            Bucket=bucket_name,
            Policy=json.dumps(bucket_policy)
        )
        
        return bucket_name
    
    def create_management_trail(self, bucket_name):
        """Create CloudTrail for management events"""
        
        trail_config = {
            'Name': 'MyLearning-Management-Trail',
            'S3BucketName': bucket_name,
            'S3KeyPrefix': 'management-events/',
            'IncludeGlobalServiceEvents': True,
            'IsMultiRegionTrail': True,
            'EnableLogFileValidation': True,
            'EventSelectors': [
                {
                    'ReadWriteType': 'All',
                    'IncludeManagementEvents': True,
                    'DataResources': []
                }
            ],
            'InsightSelectors': [
                {
                    'InsightType': 'ApiCallRateInsight'
                }
            ],
            'Tags': [
                {'Key': 'Environment', 'Value': 'production'},
                {'Key': 'Purpose', 'Value': 'compliance'},
                {'Key': 'Application', 'Value': 'MyLearning'}
            ]
        }
        
        # Create the trail
        response = self.cloudtrail.create_trail(**trail_config)
        
        # Start logging
        self.cloudtrail.start_logging(Name=trail_config['Name'])
        
        print(f"Management trail created: {response['TrailARN']}")
        return response['TrailARN']
    
    def create_data_events_trail(self, bucket_name):
        """Create CloudTrail for data events"""
        
        # Get MyLearning S3 buckets for data event logging
        s3_buckets = [
            'mylearning-course-content',
            'mylearning-user-uploads',
            'mylearning-backups'
        ]
        
        data_resources = []
        for bucket in s3_buckets:
            data_resources.extend([
                {
                    'Type': 'AWS::S3::Object',
                    'Values': [f'arn:aws:s3:::{bucket}/*']
                },
                {
                    'Type': 'AWS::S3::Bucket',
                    'Values': [f'arn:aws:s3:::{bucket}']
                }
            ])
        
        trail_config = {
            'Name': 'MyLearning-DataEvents-Trail',
            'S3BucketName': bucket_name,
            'S3KeyPrefix': 'data-events/',
            'IncludeGlobalServiceEvents': False,
            'IsMultiRegionTrail': True,
            'EnableLogFileValidation': True,
            'EventSelectors': [
                {
                    'ReadWriteType': 'All',
                    'IncludeManagementEvents': False,
                    'DataResources': data_resources
                }
            ]
        }
        
        response = self.cloudtrail.create_trail(**trail_config)
        self.cloudtrail.start_logging(Name=trail_config['Name'])
        
        print(f"Data events trail created: {response['TrailARN']}")
        return response['TrailARN']

# Initialize and configure CloudTrail
ml_cloudtrail = MyLearningCloudTrail()

# Create S3 bucket for logs
bucket_name = ml_cloudtrail.create_cloudtrail_bucket()

# Create management events trail
mgmt_trail_arn = ml_cloudtrail.create_management_trail(bucket_name)

# Create data events trail
data_trail_arn = ml_cloudtrail.create_data_events_trail(bucket_name)
```

### Advanced CloudTrail Configuration

#### Multi-Account CloudTrail Setup
```python
def setup_organization_trail():
    """Set up organization-wide CloudTrail for MyLearning.com"""
    
    # This would be run from the management account
    cloudtrail = boto3.client('cloudtrail')
    organizations = boto3.client('organizations')
    
    # Create organization trail
    org_trail_config = {
        'Name': 'MyLearning-Organization-Trail',
        'S3BucketName': 'mylearning-org-cloudtrail-logs',
        'S3KeyPrefix': 'organization-logs/',
        'IncludeGlobalServiceEvents': True,
        'IsMultiRegionTrail': True,
        'EnableLogFileValidation': True,
        'IsOrganizationTrail': True,  # Key setting for organization trail
        'EventSelectors': [
            {
                'ReadWriteType': 'All',
                'IncludeManagementEvents': True,
                'DataResources': [
                    {
                        'Type': 'AWS::S3::Object',
                        'Values': ['arn:aws:s3:::mylearning-*/*']
                    }
                ]
            }
        ],
        'InsightSelectors': [
            {
                'InsightType': 'ApiCallRateInsight'
            }
        ]
    }
    
    response = cloudtrail.create_trail(**org_trail_config)
    cloudtrail.start_logging(Name=org_trail_config['Name'])
    
    return response['TrailARN']

# MyLearning.com Account Structure
account_structure = {
    'management_account': {
        'account_id': '123456789012',
        'purpose': 'Billing and organization management',
        'cloudtrail_role': 'Organization trail management'
    },
    'production_account': {
        'account_id': '123456789013',
        'purpose': 'Production workloads',
        'cloudtrail_role': 'Detailed logging of prod activities'
    },
    'staging_account': {
        'account_id': '123456789014',
        'purpose': 'Testing and staging',
        'cloudtrail_role': 'Development activity tracking'
    },
    'security_account': {
        'account_id': '123456789015',
        'purpose': 'Security tools and monitoring',
        'cloudtrail_role': 'Centralized log analysis'
    }
}
```

## CloudTrail Log Analysis

### Understanding CloudTrail Log Format

#### Sample CloudTrail Event
```json
{
  "eventVersion": "1.08",
  "userIdentity": {
    "type": "IAMUser",
    "principalId": "AIDACKCEVSQ6C2EXAMPLE",
    "arn": "arn:aws:iam::123456789012:user/sarah.admin",
    "accountId": "123456789012",
    "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
    "userName": "sarah.admin"
  },
  "eventTime": "2024-01-15T10:30:45Z",
  "eventSource": "ec2.amazonaws.com",
  "eventName": "RunInstances",
  "awsRegion": "ap-south-1",
  "sourceIPAddress": "203.0.113.12",
  "userAgent": "aws-cli/2.0.55 Python/3.8.5 Linux/5.4.0-48-generic botocore/2.0.0dev0",
  "requestParameters": {
    "instancesSet": {
      "items": [
        {
          "imageId": "ami-0c02fb55956c7d316",
          "minCount": 1,
          "maxCount": 1,
          "instanceType": "t3.medium"
        }
      ]
    },
    "instanceType": "t3.medium",
    "keyName": "MyLearning-KeyPair",
    "securityGroupSet": {
      "items": [
        {
          "groupId": "sg-0123456789abcdef0"
        }
      ]
    },
    "subnetId": "subnet-0123456789abcdef0"
  },
  "responseElements": {
    "instancesSet": {
      "items": [
        {
          "instanceId": "i-0123456789abcdef0",
          "imageId": "ami-0c02fb55956c7d316",
          "state": {
            "code": 0,
            "name": "pending"
          },
          "privateDnsName": "",
          "privateIpAddress": "10.0.1.100",
          "instanceType": "t3.medium",
          "launchTime": "2024-01-15T10:30:45.000Z"
        }
      ]
    }
  },
  "requestID": "12345678-1234-1234-1234-123456789012",
  "eventID": "87654321-4321-4321-4321-210987654321",
  "eventType": "AwsApiCall",
  "apiVersion": "2016-11-15",
  "managementEvent": true,
  "recipientAccountId": "123456789012",
  "serviceEventDetails": {
    "responseElements": null
  }
}
```

### CloudTrail Analytics Queries

#### Common Investigation Scenarios
```python
def analyze_cloudtrail_events():
    """Common CloudTrail analysis queries for MyLearning.com"""
    
    # CloudWatch Insights queries for CloudTrail logs
    queries = {
        'failed_logins': """
            fields @timestamp, sourceIPAddress, userIdentity.userName, errorCode, errorMessage
            | filter eventName = "ConsoleLogin" and errorCode exists
            | stats count() by sourceIPAddress, userIdentity.userName
            | sort count desc
        """,
        
        'privilege_escalation': """
            fields @timestamp, userIdentity.userName, eventName, requestParameters
            | filter eventName like /Attach.*Policy/ or eventName like /Put.*Policy/
            | filter requestParameters.policyArn like /Admin/ or requestParameters.policyDocument like /\*/
            | sort @timestamp desc
        """,
        
        'resource_creation_spikes': """
            fields @timestamp, eventName, userIdentity.userName, awsRegion
            | filter eventName like /Create/ or eventName like /Run/
            | stats count() by bin(1h), eventName
            | sort @timestamp desc
        """,
        
        'unusual_api_calls': """
            fields @timestamp, eventName, sourceIPAddress, userIdentity.userName
            | filter sourceIPAddress not like /^10\./ and sourceIPAddress not like /^172\.16\./
            | stats count() by sourceIPAddress, eventName
            | sort count desc
        """,
        
        'data_access_patterns': """
            fields @timestamp, eventName, requestParameters.bucketName, requestParameters.key
            | filter eventSource = "s3.amazonaws.com" and eventName like /Get/
            | stats count() by requestParameters.bucketName, userIdentity.userName
            | sort count desc
        """,
        
        'security_group_changes': """
            fields @timestamp, eventName, userIdentity.userName, requestParameters
            | filter eventSource = "ec2.amazonaws.com" 
            | filter eventName like /.*SecurityGroup.*/
            | sort @timestamp desc
        """
    }
    
    return queries

# MyLearning.com Security Monitoring Use Cases
security_monitoring = {
    'insider_threat_detection': {
        'indicators': [
            'Unusual after-hours access',
            'Bulk data downloads',
            'Privilege escalation attempts',
            'Access from new locations'
        ],
        'cloudtrail_events': [
            'ConsoleLogin from unusual IPs',
            'S3 GetObject with large volumes',
            'IAM policy modifications',
            'Cross-region API calls'
        ]
    },
    'compliance_monitoring': {
        'requirements': [
            'All administrative actions logged',
            'Data access audit trails',
            'Change management evidence',
            'User activity tracking'
        ],
        'cloudtrail_coverage': [
            'Management events for all services',
            'Data events for sensitive buckets',
            'Multi-region trail configuration',
            'Log file integrity validation'
        ]
    },
    'incident_response': {
        'investigation_queries': [
            'Timeline of events during incident',
            'User actions before/after incident',
            'Resource modifications',
            'Network access patterns'
        ],
        'forensic_evidence': [
            'Immutable log records',
            'Cryptographic log validation',
            'Complete API call history',
            'User identity correlation'
        ]
    }
}
```

This completes the CloudTrail introduction section of Module 11. The content shows how MyLearning.com addresses their compliance and audit requirements through comprehensive CloudTrail implementation, maintaining the practical storytelling approach while covering essential CloudTrail concepts and configurations.