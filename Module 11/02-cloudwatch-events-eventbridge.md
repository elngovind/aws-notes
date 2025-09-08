# Amazon CloudWatch Events and EventBridge

## MyLearning.com's Event-Driven Architecture Evolution

### The Need for Event-Driven Automation
As MyLearning.com's platform grew, the team realized they needed automated responses to various system and business events:

```
Event-Driven Requirements:
├── System Events
│   ├── Auto Scaling activities
│   ├── EC2 instance state changes
│   ├── RDS maintenance windows
│   ├── Lambda function failures
│   └── Security group modifications
├── Application Events
│   ├── User registration completions
│   ├── Course enrollment milestones
│   ├── Payment processing events
│   ├── Content delivery failures
│   └── API rate limit breaches
├── Business Events
│   ├── Daily revenue thresholds
│   ├── Course completion celebrations
│   ├── Instructor performance metrics
│   ├── Student engagement alerts
│   └── Marketing campaign triggers
└── Compliance Events
    ├── Data backup completions
    ├── Security audit triggers
    ├── Access log archival
    └── Regulatory reporting deadlines
```

### EventBridge vs CloudWatch Events
```
Service Evolution:
├── CloudWatch Events (Legacy)
│   ├── AWS service events only
│   ├── Limited partner integrations
│   ├── Basic event patterns
│   └── Simple rule-based routing
├── Amazon EventBridge (Current)
│   ├── AWS + SaaS + custom events
│   ├── 90+ SaaS partner integrations
│   ├── Advanced event patterns
│   ├── Schema registry support
│   ├── Event replay capabilities
│   └── Cross-account event routing
└── Migration Path
    ├── Existing CloudWatch Events rules continue working
    ├── New features only in EventBridge
    ├── Gradual migration recommended
    └── No breaking changes required
```

## EventBridge Core Concepts

### Event Sources and Targets

#### MyLearning.com Event Architecture
```
EventBridge Architecture:
├── Event Sources
│   ├── AWS Services
│   │   ├── EC2 (instance state changes)
│   │   ├── RDS (backup completions)
│   │   ├── Lambda (function executions)
│   │   ├── S3 (object uploads)
│   │   └── Auto Scaling (scaling activities)
│   ├── Custom Applications
│   │   ├── User management service
│   │   ├── Course delivery platform
│   │   ├── Payment processing system
│   │   ├── Content management system
│   │   └── Analytics service
│   └── SaaS Partners
│       ├── Salesforce (lead updates)
│       ├── Stripe (payment events)
│       ├── SendGrid (email delivery)
│       ├── Zendesk (support tickets)
│       └── Datadog (monitoring alerts)
├── Event Bus
│   ├── Default bus (AWS service events)
│   ├── Custom bus (application events)
│   ├── Partner bus (SaaS events)
│   └── Cross-account buses
├── Rules and Patterns
│   ├── Event pattern matching
│   ├── Schedule-based rules
│   ├── Content-based filtering
│   └── Transformation rules
└── Targets
    ├── Lambda functions
    ├── SNS topics
    ├── SQS queues
    ├── Kinesis streams
    ├── Step Functions
    ├── ECS tasks
    └── API Gateway endpoints
```

### Event Patterns and Filtering

#### Advanced Event Pattern Examples
```json
{
  "mylearning_user_events": {
    "source": ["mylearning.user-service"],
    "detail-type": ["User Registration", "User Login", "Course Enrollment"],
    "detail": {
      "status": ["completed", "failed"],
      "user-tier": ["premium", "enterprise"],
      "region": ["ap-south-1", "us-east-1"]
    }
  },
  "aws_infrastructure_events": {
    "source": ["aws.ec2", "aws.rds", "aws.autoscaling"],
    "detail-type": ["EC2 Instance State-change Notification"],
    "detail": {
      "state": ["running", "stopped", "terminated"],
      "instance-id": [{"prefix": "i-mylearning"}]
    }
  },
  "security_events": {
    "source": ["aws.guardduty", "mylearning.security"],
    "detail-type": ["GuardDuty Finding", "Security Alert"],
    "detail": {
      "severity": [{"numeric": [">=", 7.0]}],
      "type": ["UnauthorizedAPICall", "Backdoor", "Trojan"]
    }
  },
  "business_events": {
    "source": ["mylearning.analytics"],
    "detail-type": ["Revenue Milestone", "Course Completion"],
    "detail": {
      "amount": [{"numeric": [">", 10000]}],
      "course-category": ["technology", "business", "design"]
    }
  }
}
```

## Practical Implementation

### Setting Up Custom Event Bus

#### Custom Event Bus Creation
```python
import boto3
import json
from datetime import datetime

class MyLearningEventBridge:
    def __init__(self):
        self.eventbridge = boto3.client('events')
        self.custom_bus_name = 'mylearning-application-events'
    
    def create_custom_event_bus(self):
        """Create custom event bus for MyLearning.com events"""
        
        try:
            response = self.eventbridge.create_event_bus(
                Name=self.custom_bus_name,
                Tags=[
                    {'Key': 'Environment', 'Value': 'production'},
                    {'Key': 'Application', 'Value': 'MyLearning'},
                    {'Key': 'Purpose', 'Value': 'ApplicationEvents'}
                ]
            )
            
            print(f"Custom event bus created: {response['EventBusArn']}")
            return response['EventBusArn']
            
        except Exception as e:
            print(f"Error creating event bus: {str(e)}")
            return None
    
    def put_custom_event(self, source, detail_type, detail, resources=None):
        """Publish custom events to MyLearning event bus"""
        
        event_entry = {
            'Source': source,
            'DetailType': detail_type,
            'Detail': json.dumps(detail),
            'EventBusName': self.custom_bus_name,
            'Time': datetime.utcnow()
        }
        
        if resources:
            event_entry['Resources'] = resources
        
        try:
            response = self.eventbridge.put_events(
                Entries=[event_entry]
            )
            
            if response['FailedEntryCount'] == 0:
                print(f"Event published successfully: {detail_type}")
                return True
            else:
                print(f"Failed to publish event: {response['Entries'][0]['ErrorMessage']}")
                return False
                
        except Exception as e:
            print(f"Error publishing event: {str(e)}")
            return False

# Initialize MyLearning EventBridge
ml_eventbridge = MyLearningEventBridge()

# Create custom event bus
bus_arn = ml_eventbridge.create_custom_event_bus()

# Example: User registration event
user_registration_event = {
    'user_id': 'user_12345',
    'email': 'student@example.com',
    'registration_type': 'premium',
    'course_interests': ['aws', 'python', 'devops'],
    'referral_source': 'google_ads',
    'timestamp': datetime.utcnow().isoformat()
}

ml_eventbridge.put_custom_event(
    source='mylearning.user-service',
    detail_type='User Registration Completed',
    detail=user_registration_event,
    resources=['arn:aws:lambda:ap-south-1:123456789012:function:user-onboarding']
)
```

### Event Rules and Targets

#### Comprehensive Rule Configuration
```python
def create_eventbridge_rules():
    """Create EventBridge rules for MyLearning.com automation"""
    
    eventbridge = boto3.client('events')
    
    # Rule 1: User Registration Automation
    user_registration_rule = {
        'Name': 'MyLearning-UserRegistration-Automation',
        'EventPattern': json.dumps({
            'source': ['mylearning.user-service'],
            'detail-type': ['User Registration Completed'],
            'detail': {
                'registration_type': ['premium', 'enterprise']
            }
        }),
        'State': 'ENABLED',
        'Description': 'Automate premium user onboarding process',
        'EventBusName': 'mylearning-application-events'
    }
    
    # Create the rule
    eventbridge.put_rule(**user_registration_rule)
    
    # Add targets to the rule
    eventbridge.put_targets(
        Rule='MyLearning-UserRegistration-Automation',
        EventBusName='mylearning-application-events',
        Targets=[
            {
                'Id': '1',
                'Arn': 'arn:aws:lambda:ap-south-1:123456789012:function:send-welcome-email',
                'InputTransformer': {
                    'InputPathsMap': {
                        'userId': '$.detail.user_id',
                        'email': '$.detail.email',
                        'tier': '$.detail.registration_type'
                    },
                    'InputTemplate': '{"userId": "<userId>", "email": "<email>", "tier": "<tier>", "action": "send_welcome_email"}'
                }
            },
            {
                'Id': '2',
                'Arn': 'arn:aws:sqs:ap-south-1:123456789012:user-onboarding-queue',
                'MessageGroupId': 'user-onboarding'
            },
            {
                'Id': '3',
                'Arn': 'arn:aws:sns:ap-south-1:123456789012:sales-notifications',
                'InputTransformer': {
                    'InputPathsMap': {
                        'userId': '$.detail.user_id',
                        'tier': '$.detail.registration_type',
                        'source': '$.detail.referral_source'
                    },
                    'InputTemplate': 'New premium user registered: <userId>, Tier: <tier>, Source: <source>'
                }
            }
        ]
    )
    
    # Rule 2: Infrastructure Monitoring
    infrastructure_rule = {
        'Name': 'MyLearning-Infrastructure-Monitoring',
        'EventPattern': json.dumps({
            'source': ['aws.ec2', 'aws.rds', 'aws.autoscaling'],
            'detail-type': [
                'EC2 Instance State-change Notification',
                'RDS DB Instance Event',
                'EC2 Instance Launch Successful'
            ],
            'detail': {
                'state': ['stopped', 'terminated', 'running']
            }
        }),
        'State': 'ENABLED',
        'Description': 'Monitor infrastructure changes and automate responses'
    }
    
    eventbridge.put_rule(**infrastructure_rule)
    
    eventbridge.put_targets(
        Rule='MyLearning-Infrastructure-Monitoring',
        Targets=[
            {
                'Id': '1',
                'Arn': 'arn:aws:lambda:ap-south-1:123456789012:function:infrastructure-event-processor',
                'DeadLetterConfig': {
                    'Arn': 'arn:aws:sqs:ap-south-1:123456789012:infrastructure-dlq'
                },
                'RetryPolicy': {
                    'MaximumRetryAttempts': 3,
                    'MaximumEventAge': 3600
                }
            },
            {
                'Id': '2',
                'Arn': 'arn:aws:sns:ap-south-1:123456789012:infrastructure-alerts'
            }
        ]
    )
    
    # Rule 3: Scheduled Maintenance Tasks
    maintenance_rule = {
        'Name': 'MyLearning-Daily-Maintenance',
        'ScheduleExpression': 'cron(0 2 * * ? *)',  # Daily at 2 AM UTC
        'State': 'ENABLED',
        'Description': 'Daily maintenance and cleanup tasks'
    }
    
    eventbridge.put_rule(**maintenance_rule)
    
    eventbridge.put_targets(
        Rule='MyLearning-Daily-Maintenance',
        Targets=[
            {
                'Id': '1',
                'Arn': 'arn:aws:lambda:ap-south-1:123456789012:function:daily-backup-cleanup'
            },
            {
                'Id': '2',
                'Arn': 'arn:aws:lambda:ap-south-1:123456789012:function:log-archival'
            },
            {
                'Id': '3',
                'Arn': 'arn:aws:lambda:ap-south-1:123456789012:function:performance-report-generator'
            }
        ]
    )

# Execute rule creation
create_eventbridge_rules()
```

### Lambda Function Integration

#### Event Processing Lambda Functions
```python
# Lambda Function: User Registration Event Processor
import json
import boto3
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    """Process user registration events from EventBridge"""
    
    try:
        # Parse EventBridge event
        detail = event['detail']
        user_id = detail['user_id']
        email = detail['email']
        registration_type = detail['registration_type']
        
        logger.info(f"Processing registration for user: {user_id}")
        
        # Initialize AWS services
        ses = boto3.client('ses')
        dynamodb = boto3.resource('dynamodb')
        
        # Update user profile in DynamoDB
        users_table = dynamodb.Table('MyLearning-Users')
        users_table.update_item(
            Key={'user_id': user_id},
            UpdateExpression='SET registration_status = :status, onboarding_step = :step',
            ExpressionAttributeValues={
                ':status': 'active',
                ':step': 'welcome_email_sent'
            }
        )
        
        # Send personalized welcome email
        email_template = get_welcome_email_template(registration_type)
        
        ses.send_templated_email(
            Source='welcome@mylearning.com',
            Destination={'ToAddresses': [email]},
            Template=email_template,
            TemplateData=json.dumps({
                'user_id': user_id,
                'registration_type': registration_type
            })
        )
        
        # Trigger course recommendations
        if registration_type in ['premium', 'enterprise']:
            trigger_course_recommendations(user_id, detail.get('course_interests', []))
        
        logger.info(f"Successfully processed registration for user: {user_id}")
        
        return {
            'statusCode': 200,
            'body': json.dumps({
                'message': 'User registration processed successfully',
                'user_id': user_id
            })
        }
        
    except Exception as e:
        logger.error(f"Error processing user registration: {str(e)}")
        raise e

def get_welcome_email_template(registration_type):
    """Get appropriate email template based on registration type"""
    
    templates = {
        'free': 'MyLearning-Welcome-Free',
        'premium': 'MyLearning-Welcome-Premium',
        'enterprise': 'MyLearning-Welcome-Enterprise'
    }
    
    return templates.get(registration_type, 'MyLearning-Welcome-Default')

def trigger_course_recommendations(user_id, interests):
    """Trigger personalized course recommendations"""
    
    eventbridge = boto3.client('events')
    
    recommendation_event = {
        'user_id': user_id,
        'interests': interests,
        'recommendation_type': 'onboarding',
        'priority': 'high'
    }
    
    eventbridge.put_events(
        Entries=[
            {
                'Source': 'mylearning.recommendation-service',
                'DetailType': 'Course Recommendation Request',
                'Detail': json.dumps(recommendation_event),
                'EventBusName': 'mylearning-application-events'
            }
        ]
    )
```

## Advanced EventBridge Features

### Schema Registry Integration

#### Schema Management for MyLearning.com
```python
def setup_schema_registry():
    """Set up EventBridge Schema Registry for MyLearning.com"""
    
    schemas = boto3.client('schemas')
    
    # Create schema registry
    registry_response = schemas.create_registry(
        RegistryName='MyLearning-EventSchemas',
        Description='Event schemas for MyLearning.com application events',
        Tags={
            'Environment': 'production',
            'Application': 'MyLearning'
        }
    )
    
    # Define user registration event schema
    user_registration_schema = {
        "openapi": "3.0.0",
        "info": {
            "version": "1.0.0",
            "title": "UserRegistrationEvent"
        },
        "paths": {},
        "components": {
            "schemas": {
                "UserRegistrationEvent": {
                    "type": "object",
                    "required": ["user_id", "email", "registration_type", "timestamp"],
                    "properties": {
                        "user_id": {"type": "string"},
                        "email": {"type": "string", "format": "email"},
                        "registration_type": {
                            "type": "string",
                            "enum": ["free", "premium", "enterprise"]
                        },
                        "course_interests": {
                            "type": "array",
                            "items": {"type": "string"}
                        },
                        "referral_source": {"type": "string"},
                        "timestamp": {"type": "string", "format": "date-time"}
                    }
                }
            }
        }
    }
    
    # Create schema
    schemas.create_schema(
        RegistryName='MyLearning-EventSchemas',
        SchemaName='UserRegistrationEvent',
        Type='OpenApi3',
        Description='Schema for user registration events',
        Content=json.dumps(user_registration_schema)
    )
    
    print("Schema registry and schemas created successfully")

# MyLearning.com Event Schema Catalog
event_schemas = {
    'user_events': {
        'UserRegistrationEvent': 'User registration completion',
        'UserLoginEvent': 'User authentication events',
        'CourseEnrollmentEvent': 'Course enrollment activities',
        'CourseCompletionEvent': 'Course completion milestones'
    },
    'system_events': {
        'InfrastructureChangeEvent': 'Infrastructure state changes',
        'SecurityAlertEvent': 'Security-related alerts',
        'PerformanceMetricEvent': 'Performance threshold breaches',
        'MaintenanceEvent': 'Scheduled maintenance activities'
    },
    'business_events': {
        'RevenueThresholdEvent': 'Revenue milestone achievements',
        'CustomerSupportEvent': 'Support ticket activities',
        'MarketingCampaignEvent': 'Marketing campaign triggers',
        'AnalyticsReportEvent': 'Automated report generation'
    }
}
```

### Event Replay and Archive

#### Event Replay Configuration
```python
def setup_event_replay():
    """Configure event replay for MyLearning.com"""
    
    eventbridge = boto3.client('events')
    
    # Create event archive
    archive_response = eventbridge.create_archive(
        ArchiveName='MyLearning-UserEvents-Archive',
        EventSourceArn='arn:aws:events:ap-south-1:123456789012:event-bus/mylearning-application-events',
        Description='Archive of user-related events for replay and analysis',
        EventPattern=json.dumps({
            'source': ['mylearning.user-service'],
            'detail-type': [
                'User Registration Completed',
                'Course Enrollment',
                'Course Completion'
            ]
        }),
        RetentionDays=90
    )
    
    print(f"Event archive created: {archive_response['ArchiveArn']}")
    
    # Function to replay events
    def replay_events(start_time, end_time, destination_rule):
        """Replay archived events to a specific rule"""
        
        replay_response = eventbridge.start_replay(
            ReplayName=f'MyLearning-Replay-{int(time.time())}',
            Description='Replay user events for testing new features',
            EventSourceArn=archive_response['ArchiveArn'],
            EventStartTime=start_time,
            EventEndTime=end_time,
            Destination={
                'Arn': f'arn:aws:events:ap-south-1:123456789012:rule/{destination_rule}',
                'FilterArns': []
            }
        )
        
        return replay_response['ReplayArn']
    
    return replay_events

# Event Replay Use Cases for MyLearning.com
replay_scenarios = {
    'feature_testing': {
        'description': 'Test new user onboarding features',
        'event_types': ['User Registration Completed'],
        'replay_duration': '1 week of events',
        'target_environment': 'staging'
    },
    'bug_reproduction': {
        'description': 'Reproduce payment processing issues',
        'event_types': ['Payment Processing Event'],
        'replay_duration': 'Specific time window',
        'target_environment': 'development'
    },
    'performance_testing': {
        'description': 'Load test event processing pipeline',
        'event_types': ['All user events'],
        'replay_duration': 'Peak traffic period',
        'target_environment': 'performance_test'
    }
}
```

This covers the EventBridge section of Module 11. The content demonstrates how MyLearning.com can leverage EventBridge for sophisticated event-driven automation, maintaining the practical storytelling approach while showing real-world implementation patterns.