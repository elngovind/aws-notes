# CloudTrail Practical Implementation and Best Practices

## MyLearning.com's CloudTrail Deployment Strategy

### Immediate Action Plan
After the compliance wake-up call, the MyLearning.com team developed a comprehensive CloudTrail implementation strategy:

```
CloudTrail Implementation Roadmap:
├── Phase 1: Emergency Compliance (Week 1)
│   ├── Enable basic CloudTrail in all regions
│   ├── Configure S3 bucket with proper permissions
│   ├── Set up CloudWatch Logs integration
│   └── Create initial security monitoring alerts
├── Phase 2: Enhanced Monitoring (Week 2)
│   ├── Configure data events for sensitive resources
│   ├── Set up CloudTrail Insights
│   ├── Implement automated log analysis
│   └── Create compliance reporting dashboard
├── Phase 3: Advanced Security (Week 3-4)
│   ├── Multi-account trail configuration
│   ├── Integration with SIEM tools
│   ├── Automated incident response
│   └── Comprehensive audit procedures
└── Phase 4: Optimization (Ongoing)
    ├── Cost optimization strategies
    ├── Performance tuning
    ├── Advanced analytics implementation
    └── Continuous compliance monitoring
```

## Comprehensive CloudTrail Setup

### Production-Ready CloudTrail Configuration

#### Complete Trail Setup Script
```python
import boto3
import json
import time
from datetime import datetime, timedelta

class ProductionCloudTrail:
    def __init__(self, account_id, region='ap-south-1'):
        self.account_id = account_id
        self.region = region
        self.cloudtrail = boto3.client('cloudtrail', region_name=region)
        self.s3 = boto3.client('s3', region_name=region)
        self.iam = boto3.client('iam', region_name=region)
        self.kms = boto3.client('kms', region_name=region)
        self.logs = boto3.client('logs', region_name=region)
        
    def create_kms_key_for_cloudtrail(self):
        """Create KMS key for CloudTrail log encryption"""
        
        key_policy = {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "Enable IAM User Permissions",
                    "Effect": "Allow",
                    "Principal": {"AWS": f"arn:aws:iam::{self.account_id}:root"},
                    "Action": "kms:*",
                    "Resource": "*"
                },
                {
                    "Sid": "Allow CloudTrail to encrypt logs",
                    "Effect": "Allow",
                    "Principal": {"Service": "cloudtrail.amazonaws.com"},
                    "Action": [
                        "kms:GenerateDataKey*",
                        "kms:DescribeKey"
                    ],
                    "Resource": "*"
                },
                {
                    "Sid": "Allow CloudTrail to describe key",
                    "Effect": "Allow",
                    "Principal": {"Service": "cloudtrail.amazonaws.com"},
                    "Action": "kms:DescribeKey",
                    "Resource": "*"
                }
            ]
        }
        
        response = self.kms.create_key(
            Policy=json.dumps(key_policy),
            Description='MyLearning CloudTrail Log Encryption Key',
            Usage='ENCRYPT_DECRYPT',
            KeySpec='SYMMETRIC_DEFAULT'
        )
        
        key_id = response['KeyMetadata']['KeyId']
        
        # Create alias for the key
        self.kms.create_alias(
            AliasName='alias/mylearning-cloudtrail-key',
            TargetKeyId=key_id
        )
        
        return response['KeyMetadata']['Arn']
    
    def create_cloudtrail_s3_bucket(self):
        """Create S3 bucket with security best practices"""
        
        bucket_name = f'mylearning-cloudtrail-{self.account_id}-{self.region}'
        
        # Create bucket with encryption
        if self.region == 'us-east-1':
            self.s3.create_bucket(Bucket=bucket_name)
        else:
            self.s3.create_bucket(
                Bucket=bucket_name,
                CreateBucketConfiguration={'LocationConstraint': self.region}
            )
        
        # Enable versioning
        self.s3.put_bucket_versioning(
            Bucket=bucket_name,
            VersioningConfiguration={'Status': 'Enabled'}
        )
        
        # Enable MFA delete (requires root user)
        # This would need to be done via CLI with root credentials
        
        # Configure lifecycle policy
        lifecycle_config = {
            'Rules': [
                {
                    'ID': 'CloudTrailLogRetention',
                    'Status': 'Enabled',
                    'Filter': {'Prefix': 'AWSLogs/'},
                    'Transitions': [
                        {
                            'Days': 30,
                            'StorageClass': 'STANDARD_IA'
                        },
                        {
                            'Days': 90,
                            'StorageClass': 'GLACIER'
                        },
                        {
                            'Days': 2555,  # 7 years
                            'StorageClass': 'DEEP_ARCHIVE'
                        }
                    ],
                    'Expiration': {'Days': 3650}  # 10 years retention
                }
            ]
        }
        
        self.s3.put_bucket_lifecycle_configuration(
            Bucket=bucket_name,
            LifecycleConfiguration=lifecycle_config
        )
        
        # Block public access
        self.s3.put_public_access_block(
            Bucket=bucket_name,
            PublicAccessBlockConfiguration={
                'BlockPublicAcls': True,
                'IgnorePublicAcls': True,
                'BlockPublicPolicy': True,
                'RestrictPublicBuckets': True
            }
        )
        
        # Configure bucket policy
        bucket_policy = {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "AWSCloudTrailAclCheck",
                    "Effect": "Allow",
                    "Principal": {"Service": "cloudtrail.amazonaws.com"},
                    "Action": "s3:GetBucketAcl",
                    "Resource": f"arn:aws:s3:::{bucket_name}",
                    "Condition": {
                        "StringEquals": {
                            "AWS:SourceArn": f"arn:aws:cloudtrail:{self.region}:{self.account_id}:trail/MyLearning-Production-Trail"
                        }
                    }
                },
                {
                    "Sid": "AWSCloudTrailWrite",
                    "Effect": "Allow",
                    "Principal": {"Service": "cloudtrail.amazonaws.com"},
                    "Action": "s3:PutObject",
                    "Resource": f"arn:aws:s3:::{bucket_name}/AWSLogs/{self.account_id}/*",
                    "Condition": {
                        "StringEquals": {
                            "s3:x-amz-acl": "bucket-owner-full-control",
                            "AWS:SourceArn": f"arn:aws:cloudtrail:{self.region}:{self.account_id}:trail/MyLearning-Production-Trail"
                        }
                    }
                },
                {
                    "Sid": "DenyInsecureConnections",
                    "Effect": "Deny",
                    "Principal": "*",
                    "Action": "s3:*",
                    "Resource": [
                        f"arn:aws:s3:::{bucket_name}",
                        f"arn:aws:s3:::{bucket_name}/*"
                    ],
                    "Condition": {
                        "Bool": {"aws:SecureTransport": "false"}
                    }
                }
            ]
        }
        
        self.s3.put_bucket_policy(
            Bucket=bucket_name,
            Policy=json.dumps(bucket_policy)
        )
        
        return bucket_name
    
    def create_cloudwatch_log_group(self):
        """Create CloudWatch Log Group for CloudTrail"""
        
        log_group_name = '/aws/cloudtrail/mylearning-production'
        
        try:
            self.logs.create_log_group(
                logGroupName=log_group_name,
                kmsKeyId='alias/mylearning-cloudtrail-key'
            )
            
            # Set retention policy
            self.logs.put_retention_policy(
                logGroupName=log_group_name,
                retentionInDays=90
            )
            
        except self.logs.exceptions.ResourceAlreadyExistsException:
            print(f"Log group {log_group_name} already exists")
        
        return log_group_name
    
    def create_cloudtrail_role(self):
        """Create IAM role for CloudTrail CloudWatch Logs"""
        
        trust_policy = {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Principal": {"Service": "cloudtrail.amazonaws.com"},
                    "Action": "sts:AssumeRole"
                }
            ]
        }
        
        role_name = 'MyLearning-CloudTrail-CloudWatchLogs-Role'
        
        try:
            role_response = self.iam.create_role(
                RoleName=role_name,
                AssumeRolePolicyDocument=json.dumps(trust_policy),
                Description='Role for CloudTrail to write to CloudWatch Logs'
            )
        except self.iam.exceptions.EntityAlreadyExistsException:
            role_response = self.iam.get_role(RoleName=role_name)
        
        # Attach policy for CloudWatch Logs
        policy_document = {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": [
                        "logs:PutLogEvents",
                        "logs:CreateLogGroup",
                        "logs:CreateLogStream"
                    ],
                    "Resource": f"arn:aws:logs:{self.region}:{self.account_id}:log-group:/aws/cloudtrail/mylearning-production:*"
                }
            ]
        }
        
        policy_name = 'MyLearning-CloudTrail-CloudWatchLogs-Policy'
        
        try:
            self.iam.create_policy(
                PolicyName=policy_name,
                PolicyDocument=json.dumps(policy_document),
                Description='Policy for CloudTrail CloudWatch Logs access'
            )
        except self.iam.exceptions.EntityAlreadyExistsException:
            pass
        
        self.iam.attach_role_policy(
            RoleName=role_name,
            PolicyArn=f'arn:aws:iam::{self.account_id}:policy/{policy_name}'
        )
        
        return f'arn:aws:iam::{self.account_id}:role/{role_name}'
    
    def create_production_trail(self):
        """Create production CloudTrail with all security features"""
        
        # Create supporting resources
        kms_key_arn = self.create_kms_key_for_cloudtrail()
        bucket_name = self.create_cloudtrail_s3_bucket()
        log_group_name = self.create_cloudwatch_log_group()
        cloudwatch_role_arn = self.create_cloudtrail_role()
        
        # Wait for resources to be ready
        time.sleep(30)
        
        # Define sensitive S3 buckets for data events
        sensitive_buckets = [
            'mylearning-course-content',
            'mylearning-user-data',
            'mylearning-financial-records',
            'mylearning-backups'
        ]
        
        data_resources = []
        for bucket in sensitive_buckets:
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
        
        # Add Lambda function data events
        data_resources.append({
            'Type': 'AWS::Lambda::Function',
            'Values': ['arn:aws:lambda:*']
        })
        
        trail_config = {
            'Name': 'MyLearning-Production-Trail',
            'S3BucketName': bucket_name,
            'S3KeyPrefix': 'production-logs/',
            'IncludeGlobalServiceEvents': True,
            'IsMultiRegionTrail': True,
            'EnableLogFileValidation': True,
            'KMSKeyId': kms_key_arn,
            'CloudWatchLogsLogGroupArn': f'arn:aws:logs:{self.region}:{self.account_id}:log-group:{log_group_name}:*',
            'CloudWatchLogsRoleArn': cloudwatch_role_arn,
            'EventSelectors': [
                {
                    'ReadWriteType': 'All',
                    'IncludeManagementEvents': True,
                    'DataResources': data_resources,
                    'ExcludeManagementEventSources': [
                        'kms.amazonaws.com',
                        'rdsdata.amazonaws.com'
                    ]
                }
            ],
            'InsightSelectors': [
                {
                    'InsightType': 'ApiCallRateInsight'
                }
            ],
            'Tags': [
                {'Key': 'Environment', 'Value': 'production'},
                {'Key': 'Compliance', 'Value': 'required'},
                {'Key': 'DataClassification', 'Value': 'sensitive'},
                {'Key': 'RetentionPeriod', 'Value': '10years'}
            ]
        }
        
        # Create the trail
        response = self.cloudtrail.create_trail(**trail_config)
        
        # Start logging
        self.cloudtrail.start_logging(Name=trail_config['Name'])
        
        print(f"Production CloudTrail created: {response['TrailARN']}")
        return response
    
    def verify_trail_status(self, trail_name):
        """Verify CloudTrail is working correctly"""
        
        # Check trail status
        status = self.cloudtrail.get_trail_status(Name=trail_name)
        
        verification_results = {
            'is_logging': status['IsLogging'],
            'latest_delivery_time': status.get('LatestDeliveryTime'),
            'latest_notification_time': status.get('LatestNotificationTime'),
            'start_logging_time': status.get('StartLoggingTime'),
            'stop_logging_time': status.get('StopLoggingTime'),
            'latest_cloud_watch_logs_delivery_time': status.get('LatestCloudWatchLogsDeliveryTime')
        }
        
        # Check for any delivery errors
        if 'LatestDeliveryError' in status:
            verification_results['delivery_error'] = status['LatestDeliveryError']
        
        if 'LatestCloudWatchLogsDeliveryError' in status:
            verification_results['cloudwatch_error'] = status['LatestCloudWatchLogsDeliveryError']
        
        return verification_results

# Deploy production CloudTrail
production_trail = ProductionCloudTrail('123456789012')
trail_response = production_trail.create_production_trail()

# Verify deployment
verification = production_trail.verify_trail_status('MyLearning-Production-Trail')
print(f"Trail verification: {verification}")
```

## CloudTrail Monitoring and Alerting

### Real-Time Security Monitoring

#### CloudWatch Alarms for CloudTrail Events
```python
def create_security_monitoring_alarms():
    """Create CloudWatch alarms for security events"""
    
    cloudwatch = boto3.client('cloudwatch')
    
    # Alarm configurations for MyLearning.com security monitoring
    security_alarms = [
        {
            'AlarmName': 'MyLearning-RootAccountUsage',
            'AlarmDescription': 'Alert when root account is used',
            'MetricName': 'RootAccountUsageCount',
            'Namespace': 'MyLearning/Security',
            'Statistic': 'Sum',
            'Period': 300,
            'EvaluationPeriods': 1,
            'Threshold': 1,
            'ComparisonOperator': 'GreaterThanOrEqualToThreshold',
            'TreatMissingData': 'notBreaching'
        },
        {
            'AlarmName': 'MyLearning-UnauthorizedAPICalls',
            'AlarmDescription': 'Alert on unauthorized API calls',
            'MetricName': 'UnauthorizedAPICallsCount',
            'Namespace': 'MyLearning/Security',
            'Statistic': 'Sum',
            'Period': 300,
            'EvaluationPeriods': 2,
            'Threshold': 5,
            'ComparisonOperator': 'GreaterThanThreshold'
        },
        {
            'AlarmName': 'MyLearning-IAMPolicyChanges',
            'AlarmDescription': 'Alert on IAM policy changes',
            'MetricName': 'IAMPolicyChangesCount',
            'Namespace': 'MyLearning/Security',
            'Statistic': 'Sum',
            'Period': 300,
            'EvaluationPeriods': 1,
            'Threshold': 1,
            'ComparisonOperator': 'GreaterThanOrEqualToThreshold'
        },
        {
            'AlarmName': 'MyLearning-ConsoleSignInFailures',
            'AlarmDescription': 'Alert on console sign-in failures',
            'MetricName': 'ConsoleSignInFailuresCount',
            'Namespace': 'MyLearning/Security',
            'Statistic': 'Sum',
            'Period': 300,
            'EvaluationPeriods': 3,
            'Threshold': 10,
            'ComparisonOperator': 'GreaterThanThreshold'
        }
    ]
    
    for alarm_config in security_alarms:
        cloudwatch.put_metric_alarm(
            AlarmName=alarm_config['AlarmName'],
            ComparisonOperator=alarm_config['ComparisonOperator'],
            EvaluationPeriods=alarm_config['EvaluationPeriods'],
            MetricName=alarm_config['MetricName'],
            Namespace=alarm_config['Namespace'],
            Period=alarm_config['Period'],
            Statistic=alarm_config['Statistic'],
            Threshold=alarm_config['Threshold'],
            ActionsEnabled=True,
            AlarmActions=[
                'arn:aws:sns:ap-south-1:123456789012:security-alerts'
            ],
            AlarmDescription=alarm_config['AlarmDescription'],
            TreatMissingData=alarm_config.get('TreatMissingData', 'breaching'),
            Tags=[
                {'Key': 'Purpose', 'Value': 'SecurityMonitoring'},
                {'Key': 'Severity', 'Value': 'High'}
            ]
        )

# Create metric filters for CloudTrail logs
def create_cloudtrail_metric_filters():
    """Create CloudWatch metric filters for CloudTrail events"""
    
    logs = boto3.client('logs')
    log_group_name = '/aws/cloudtrail/mylearning-production'
    
    metric_filters = [
        {
            'filterName': 'RootAccountUsage',
            'filterPattern': '{ $.userIdentity.type = "Root" && $.userIdentity.invokedBy NOT EXISTS && $.eventType != "AwsServiceEvent" }',
            'metricTransformations': [
                {
                    'metricName': 'RootAccountUsageCount',
                    'metricNamespace': 'MyLearning/Security',
                    'metricValue': '1'
                }
            ]
        },
        {
            'filterName': 'UnauthorizedAPICalls',
            'filterPattern': '{ ($.errorCode = "*UnauthorizedOperation") || ($.errorCode = "AccessDenied*") }',
            'metricTransformations': [
                {
                    'metricName': 'UnauthorizedAPICallsCount',
                    'metricNamespace': 'MyLearning/Security',
                    'metricValue': '1'
                }
            ]
        },
        {
            'filterName': 'IAMPolicyChanges',
            'filterPattern': '{ ($.eventName=DeleteGroupPolicy) || ($.eventName=DeleteRolePolicy) || ($.eventName=DeleteUserPolicy) || ($.eventName=PutGroupPolicy) || ($.eventName=PutRolePolicy) || ($.eventName=PutUserPolicy) || ($.eventName=CreatePolicy) || ($.eventName=DeletePolicy) || ($.eventName=CreatePolicyVersion) || ($.eventName=DeletePolicyVersion) || ($.eventName=AttachRolePolicy) || ($.eventName=DetachRolePolicy) || ($.eventName=AttachUserPolicy) || ($.eventName=DetachUserPolicy) || ($.eventName=AttachGroupPolicy) || ($.eventName=DetachGroupPolicy) }',
            'metricTransformations': [
                {
                    'metricName': 'IAMPolicyChangesCount',
                    'metricNamespace': 'MyLearning/Security',
                    'metricValue': '1'
                }
            ]
        },
        {
            'filterName': 'ConsoleSignInFailures',
            'filterPattern': '{ ($.eventName = ConsoleLogin) && ($.errorMessage EXISTS) }',
            'metricTransformations': [
                {
                    'metricName': 'ConsoleSignInFailuresCount',
                    'metricNamespace': 'MyLearning/Security',
                    'metricValue': '1'
                }
            ]
        }
    ]
    
    for filter_config in metric_filters:
        logs.put_metric_filter(
            logGroupName=log_group_name,
            filterName=filter_config['filterName'],
            filterPattern=filter_config['filterPattern'],
            metricTransformations=filter_config['metricTransformations']
        )

# Execute monitoring setup
create_cloudtrail_metric_filters()
create_security_monitoring_alarms()
```

### Automated Incident Response

#### Lambda-Based Security Response
```python
import json
import boto3
import os
from datetime import datetime

def lambda_handler(event, context):
    """Automated response to CloudTrail security events"""
    
    # Parse SNS message from CloudWatch alarm
    message = json.loads(event['Records'][0]['Sns']['Message'])
    alarm_name = message['AlarmName']
    
    # Initialize AWS clients
    iam = boto3.client('iam')
    sns = boto3.client('sns')
    ses = boto3.client('ses')
    
    response_actions = {
        'MyLearning-RootAccountUsage': handle_root_account_usage,
        'MyLearning-UnauthorizedAPICalls': handle_unauthorized_api_calls,
        'MyLearning-IAMPolicyChanges': handle_iam_policy_changes,
        'MyLearning-ConsoleSignInFailures': handle_console_failures
    }
    
    if alarm_name in response_actions:
        result = response_actions[alarm_name](message)
        
        # Send notification to security team
        send_security_notification(alarm_name, message, result)
        
        return {
            'statusCode': 200,
            'body': json.dumps({
                'alarm': alarm_name,
                'action_taken': result['action'],
                'timestamp': datetime.utcnow().isoformat()
            })
        }
    
    return {'statusCode': 200, 'body': 'No action required'}

def handle_root_account_usage(alarm_message):
    """Handle root account usage detection"""
    
    # Immediate actions for root account usage
    actions_taken = []
    
    # 1. Send immediate alert to security team
    sns = boto3.client('sns')
    sns.publish(
        TopicArn='arn:aws:sns:ap-south-1:123456789012:critical-security-alerts',
        Subject='CRITICAL: Root Account Usage Detected',
        Message=f'Root account activity detected at {datetime.utcnow()}. Immediate investigation required.'
    )
    actions_taken.append('Critical alert sent')
    
    # 2. Create support case (if Business/Enterprise support)
    # This would require AWS Support API
    
    # 3. Log incident in security system
    # Integration with security incident management system
    
    return {
        'action': 'root_account_response',
        'severity': 'critical',
        'actions_taken': actions_taken
    }

def handle_unauthorized_api_calls(alarm_message):
    """Handle unauthorized API call attempts"""
    
    actions_taken = []
    
    # Query CloudTrail for recent unauthorized calls
    cloudtrail = boto3.client('cloudtrail')
    
    # Look up recent events (last 15 minutes)
    end_time = datetime.utcnow()
    start_time = end_time - timedelta(minutes=15)
    
    events = cloudtrail.lookup_events(
        LookupAttributes=[
            {
                'AttributeKey': 'EventName',
                'AttributeValue': 'UnauthorizedOperation'
            }
        ],
        StartTime=start_time,
        EndTime=end_time
    )
    
    # Analyze source IPs and users
    suspicious_ips = set()
    suspicious_users = set()
    
    for event in events['Events']:
        if 'SourceIPAddress' in event:
            suspicious_ips.add(event['SourceIPAddress'])
        if 'Username' in event:
            suspicious_users.add(event['Username'])
    
    # Take protective actions
    if suspicious_users:
        # Temporarily disable suspicious users (if not critical service accounts)
        for user in suspicious_users:
            if not user.startswith('service-'):
                try:
                    iam = boto3.client('iam')
                    # Add user to quarantine group with minimal permissions
                    iam.add_user_to_group(
                        GroupName='SecurityQuarantine',
                        UserName=user
                    )
                    actions_taken.append(f'User {user} quarantined')
                except Exception as e:
                    actions_taken.append(f'Failed to quarantine {user}: {str(e)}')
    
    return {
        'action': 'unauthorized_api_response',
        'severity': 'high',
        'suspicious_ips': list(suspicious_ips),
        'suspicious_users': list(suspicious_users),
        'actions_taken': actions_taken
    }

def handle_iam_policy_changes(alarm_message):
    """Handle IAM policy modification detection"""
    
    actions_taken = []
    
    # Query recent IAM changes
    cloudtrail = boto3.client('cloudtrail')
    
    iam_events = cloudtrail.lookup_events(
        LookupAttributes=[
            {
                'AttributeKey': 'EventName',
                'AttributeValue': 'PutUserPolicy'
            }
        ],
        StartTime=datetime.utcnow() - timedelta(minutes=10),
        EndTime=datetime.utcnow()
    )
    
    # Analyze policy changes for privilege escalation
    for event in iam_events['Events']:
        event_detail = json.loads(event['CloudTrailEvent'])
        
        # Check for admin policy attachments
        if 'requestParameters' in event_detail:
            policy_doc = event_detail['requestParameters'].get('policyDocument', '')
            if '"*"' in policy_doc and '"Effect": "Allow"' in policy_doc:
                # Potential privilege escalation detected
                actions_taken.append(f'Privilege escalation detected in event {event["EventId"]}')
                
                # Create incident ticket
                create_security_incident({
                    'type': 'privilege_escalation',
                    'event_id': event['EventId'],
                    'user': event_detail.get('userIdentity', {}).get('userName', 'Unknown'),
                    'timestamp': event['EventTime']
                })
    
    return {
        'action': 'iam_policy_response',
        'severity': 'medium',
        'actions_taken': actions_taken
    }

def send_security_notification(alarm_name, alarm_message, response_result):
    """Send detailed security notification"""
    
    ses = boto3.client('ses')
    
    email_body = f"""
    Security Alert: {alarm_name}
    
    Alarm Details:
    - Trigger Time: {alarm_message.get('StateChangeTime', 'Unknown')}
    - Alarm Description: {alarm_message.get('AlarmDescription', 'N/A')}
    - Current State: {alarm_message.get('NewStateValue', 'Unknown')}
    
    Automated Response:
    - Action Taken: {response_result['action']}
    - Severity: {response_result['severity']}
    - Details: {json.dumps(response_result, indent=2)}
    
    Please investigate immediately and follow up with additional manual actions if required.
    
    MyLearning.com Security Team
    """
    
    ses.send_email(
        Source='security@mylearning.com',
        Destination={
            'ToAddresses': [
                'security-team@mylearning.com',
                'ops-team@mylearning.com'
            ]
        },
        Message={
            'Subject': {'Data': f'Security Alert: {alarm_name}'},
            'Body': {'Text': {'Data': email_body}}
        }
    )

def create_security_incident(incident_data):
    """Create security incident in tracking system"""
    
    # This would integrate with your incident management system
    # For example: ServiceNow, Jira, PagerDuty, etc.
    
    incident_payload = {
        'title': f'Security Incident: {incident_data["type"]}',
        'description': f'Automated detection of {incident_data["type"]}',
        'severity': 'high',
        'category': 'security',
        'source': 'cloudtrail_automation',
        'details': incident_data
    }
    
    # Example integration with external system
    # requests.post('https://incident-system.mylearning.com/api/incidents', 
    #               json=incident_payload, 
    #               headers={'Authorization': 'Bearer <token>'})
    
    print(f"Security incident created: {incident_payload}")
```

## CloudTrail Cost Optimization

### Cost Management Strategies

#### Intelligent Log Management
```python
def optimize_cloudtrail_costs():
    """Implement cost optimization for CloudTrail"""
    
    cost_optimization_strategies = {
        'data_event_filtering': {
            'description': 'Filter data events to essential resources only',
            'implementation': [
                'Monitor only production S3 buckets',
                'Exclude read-only operations for non-sensitive data',
                'Focus on write operations for audit compliance',
                'Use advanced event selectors for granular control'
            ],
            'estimated_savings': '40-60% on data event costs'
        },
        
        'log_lifecycle_management': {
            'description': 'Optimize S3 storage costs for CloudTrail logs',
            'implementation': [
                'Transition to IA after 30 days',
                'Move to Glacier after 90 days',
                'Archive to Deep Archive after 1 year',
                'Set appropriate retention policies'
            ],
            'estimated_savings': '70-80% on storage costs'
        },
        
        'regional_optimization': {
            'description': 'Optimize multi-region trail configuration',
            'implementation': [
                'Use single multi-region trail instead of multiple trails',
                'Consolidate logs in primary region',
                'Avoid duplicate logging across regions',
                'Use cross-region replication only when required'
            ],
            'estimated_savings': '50-70% on trail management costs'
        },
        
        'insight_events_tuning': {
            'description': 'Optimize CloudTrail Insights usage',
            'implementation': [
                'Enable insights only for critical services',
                'Monitor insights costs vs. value',
                'Adjust sensitivity thresholds',
                'Use insights selectively for high-value resources'
            ],
            'estimated_savings': '30-50% on insights costs'
        }
    }
    
    return cost_optimization_strategies

# MyLearning.com Cost Optimization Implementation
def implement_cost_optimized_trail():
    """Create cost-optimized CloudTrail configuration"""
    
    cloudtrail = boto3.client('cloudtrail')
    
    # Optimized event selectors
    optimized_selectors = [
        {
            'ReadWriteType': 'WriteOnly',  # Only write operations for most resources
            'IncludeManagementEvents': True,
            'DataResources': [
                {
                    'Type': 'AWS::S3::Object',
                    'Values': [
                        'arn:aws:s3:::mylearning-course-content/*',  # Critical content
                        'arn:aws:s3:::mylearning-user-data/*'       # User data
                    ]
                }
            ],
            'ExcludeManagementEventSources': [
                'kms.amazonaws.com',      # Exclude noisy KMS events
                'rdsdata.amazonaws.com'   # Exclude RDS Data API events
            ]
        },
        {
            'ReadWriteType': 'All',  # All operations for sensitive resources
            'IncludeManagementEvents': False,
            'DataResources': [
                {
                    'Type': 'AWS::S3::Object',
                    'Values': [
                        'arn:aws:s3:::mylearning-financial-records/*'  # Financial data
                    ]
                }
            ]
        }
    ]
    
    # Update existing trail with optimized configuration
    cloudtrail.put_event_selectors(
        TrailName='MyLearning-Production-Trail',
        EventSelectors=optimized_selectors
    )
    
    # Configure selective insights
    cloudtrail.put_insight_selectors(
        TrailName='MyLearning-Production-Trail',
        InsightSelectors=[
            {
                'InsightType': 'ApiCallRateInsight'
            }
        ]
    )
    
    print("Cost-optimized CloudTrail configuration applied")

# Monthly cost monitoring
monthly_cost_targets = {
    'cloudtrail_management_events': '$50',
    'cloudtrail_data_events': '$200',
    'cloudtrail_insights': '$100',
    's3_storage_costs': '$150',
    'total_monthly_target': '$500'
}
```

This completes Module 11 with comprehensive coverage of advanced CloudWatch concepts, EventBridge automation, and CloudTrail implementation. The content maintains the MyLearning.com storyline while providing practical, production-ready implementations for monitoring, compliance, and security.