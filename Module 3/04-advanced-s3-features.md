# Advanced S3 Features and Integration

## Chapter 27: S3 Event Notifications and Automation (August 2022)

### The Content Processing Challenge
As MyLearning.com grew, manual content processing became a bottleneck:

```
Content Processing Challenges:
├── Video Upload Workflow
│   ├── Manual upload to S3
│   ├── Manual transcoding for different qualities
│   ├── Manual thumbnail generation
│   ├── Manual metadata extraction
│   ├── Manual content moderation
│   └── Manual notification to content team
├── Document Processing
│   ├── PDF optimization for web viewing
│   ├── Text extraction for search indexing
│   ├── Thumbnail generation for previews
│   ├── Virus scanning for security
│   └── Metadata extraction and tagging
├── Image Processing
│   ├── Multiple size generation (thumbnails, previews)
│   ├── Format optimization (WebP conversion)
│   ├── Compression for faster loading
│   ├── Watermark addition for branding
│   └── Alt text generation for accessibility
├── Business Impact
│   ├── 4-6 hours manual processing per video
│   ├── Content team bottleneck (3 people)
│   ├── Delayed course launches
│   ├── Inconsistent quality standards
│   └── High operational costs
└── Scaling Problems
    ├── Cannot handle 100+ uploads/day
    ├── Weekend and night uploads delayed
    ├── Human errors in processing
    └── No standardized workflow
```

### Discovering S3 Event Notifications
Raj discovered S3's event notification system during an AWS re:Invent session:

```
S3 Event Notification Capabilities:
├── Supported Events
│   ├── Object Created (PUT, POST, COPY, Multipart)
│   ├── Object Removed (DELETE, DELETE Marker)
│   ├── Object Restore (from Glacier)
│   ├── Reduced Redundancy Storage (RRS) object lost
│   ├── Replication events
│   └── Lifecycle transition events
├── Notification Destinations
│   ├── Amazon SNS (Simple Notification Service)
│   ├── Amazon SQS (Simple Queue Service)
│   ├── AWS Lambda Functions
│   ├── Amazon EventBridge
│   └── Third-party webhooks (via Lambda)
├── Event Filtering
│   ├── Prefix filtering (folder-based)
│   ├── Suffix filtering (file extension)
│   ├── Object size filtering
│   ├── Metadata-based filtering
│   └── Combination filters
├── Use Cases
│   ├── Automated content processing
│   ├── Real-time data analytics
│   ├── Backup and archival workflows
│   ├── Content moderation pipelines
│   ├── Notification systems
│   └── Integration with external systems
└── Benefits
    ├── Real-time processing
    ├── Serverless architecture
    ├── Cost-effective scaling
    ├── Reduced manual intervention
    └── Improved reliability
```

### Implementing Automated Content Processing
```python
# Lambda function for video processing automation
import json
import boto3
import urllib.parse
from datetime import datetime

def lambda_handler(event, context):
    """
    Triggered when video files are uploaded to S3
    Initiates automated processing pipeline
    """
    
    # Initialize AWS services
    s3_client = boto3.client('s3')
    mediaconvert_client = boto3.client('mediaconvert')
    sns_client = boto3.client('sns')
    
    # Process each S3 event record
    for record in event['Records']:
        # Extract bucket and object information
        bucket_name = record['s3']['bucket']['name']
        object_key = urllib.parse.unquote_plus(
            record['s3']['object']['key'], 
            encoding='utf-8'
        )
        
        print(f"Processing: {bucket_name}/{object_key}")
        
        # Check if it's a video file
        if not is_video_file(object_key):
            print(f"Skipping non-video file: {object_key}")
            continue
            
        try:
            # Extract metadata from S3 object
            metadata = extract_video_metadata(s3_client, bucket_name, object_key)
            
            # Start video transcoding job
            job_id = start_transcoding_job(
                mediaconvert_client, 
                bucket_name, 
                object_key, 
                metadata
            )
            
            # Generate thumbnail
            thumbnail_key = generate_thumbnail(
                s3_client, 
                bucket_name, 
                object_key
            )
            
            # Update database with processing status
            update_content_database(object_key, job_id, 'processing')
            
            # Send notification to content team
            send_processing_notification(
                sns_client, 
                object_key, 
                job_id, 
                'started'
            )
            
            print(f"Successfully initiated processing for {object_key}")
            
        except Exception as e:
            print(f"Error processing {object_key}: {str(e)}")
            
            # Send error notification
            send_error_notification(sns_client, object_key, str(e))
            
    return {
        'statusCode': 200,
        'body': json.dumps('Processing completed successfully')
    }

def is_video_file(object_key):
    """Check if the uploaded file is a video"""
    video_extensions = ['.mp4', '.avi', '.mov', '.mkv', '.wmv', '.flv']
    return any(object_key.lower().endswith(ext) for ext in video_extensions)

def extract_video_metadata(s3_client, bucket, key):
    """Extract metadata from video file"""
    try:
        response = s3_client.head_object(Bucket=bucket, Key=key)
        
        metadata = {
            'file_size': response['ContentLength'],
            'last_modified': response['LastModified'].isoformat(),
            'content_type': response.get('ContentType', 'unknown'),
            'upload_time': datetime.utcnow().isoformat(),
            'original_name': key.split('/')[-1]
        }
        
        # Extract custom metadata if present
        if 'Metadata' in response:
            metadata.update(response['Metadata'])
            
        return metadata
        
    except Exception as e:
        print(f"Error extracting metadata: {str(e)}")
        return {}

def start_transcoding_job(mediaconvert_client, bucket, key, metadata):
    """Start AWS MediaConvert job for video transcoding"""
    
    # Define output settings for different quality levels
    job_settings = {
        "Role": "arn:aws:iam::123456789012:role/MediaConvertRole",
        "Settings": {
            "Inputs": [{
                "FileInput": f"s3://{bucket}/{key}",
                "AudioSelectors": {
                    "Audio Selector 1": {
                        "DefaultSelection": "DEFAULT"
                    }
                },
                "VideoSelector": {}
            }],
            "OutputGroups": [
                {
                    "Name": "File Group",
                    "OutputGroupSettings": {
                        "Type": "FILE_GROUP_SETTINGS",
                        "FileGroupSettings": {
                            "Destination": f"s3://{bucket}/processed/{key.split('.')[0]}/"
                        }
                    },
                    "Outputs": [
                        {
                            "NameModifier": "_1080p",
                            "VideoDescription": {
                                "CodecSettings": {
                                    "Codec": "H_264",
                                    "H264Settings": {
                                        "MaxBitrate": 5000000,
                                        "RateControlMode": "QVBR"
                                    }
                                },
                                "Width": 1920,
                                "Height": 1080
                            },
                            "AudioDescriptions": [{
                                "CodecSettings": {
                                    "Codec": "AAC",
                                    "AacSettings": {
                                        "Bitrate": 128000,
                                        "SampleRate": 48000
                                    }
                                }
                            }],
                            "ContainerSettings": {
                                "Container": "MP4"
                            }
                        },
                        {
                            "NameModifier": "_720p",
                            "VideoDescription": {
                                "CodecSettings": {
                                    "Codec": "H_264",
                                    "H264Settings": {
                                        "MaxBitrate": 3000000,
                                        "RateControlMode": "QVBR"
                                    }
                                },
                                "Width": 1280,
                                "Height": 720
                            },
                            "AudioDescriptions": [{
                                "CodecSettings": {
                                    "Codec": "AAC",
                                    "AacSettings": {
                                        "Bitrate": 128000,
                                        "SampleRate": 48000
                                    }
                                }
                            }],
                            "ContainerSettings": {
                                "Container": "MP4"
                            }
                        }
                    ]
                }
            ]
        }
    }
    
    # Submit the job
    response = mediaconvert_client.create_job(**job_settings)
    return response['Job']['Id']

def update_content_database(object_key, job_id, status):
    """Update content processing status in database"""
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('ContentProcessingStatus')
    
    table.put_item(
        Item={
            'content_id': object_key,
            'job_id': job_id,
            'status': status,
            'timestamp': datetime.utcnow().isoformat(),
            'processing_stage': 'transcoding'
        }
    )

def send_processing_notification(sns_client, object_key, job_id, status):
    """Send notification about processing status"""
    message = {
        'content': object_key,
        'job_id': job_id,
        'status': status,
        'timestamp': datetime.utcnow().isoformat()
    }
    
    sns_client.publish(
        TopicArn='arn:aws:sns:ap-south-1:123456789012:content-processing-notifications',
        Message=json.dumps(message),
        Subject=f'Content Processing {status.title()}: {object_key}'
    )
```

### S3 Event Configuration
```json
{
    "LambdaConfigurations": [
        {
            "Id": "VideoProcessingTrigger",
            "LambdaFunctionArn": "arn:aws:lambda:ap-south-1:123456789012:function:ProcessVideoUpload",
            "Events": ["s3:ObjectCreated:*"],
            "Filter": {
                "Key": {
                    "FilterRules": [
                        {
                            "Name": "prefix",
                            "Value": "uploads/videos/"
                        },
                        {
                            "Name": "suffix",
                            "Value": ".mp4"
                        }
                    ]
                }
            }
        },
        {
            "Id": "DocumentProcessingTrigger",
            "LambdaFunctionArn": "arn:aws:lambda:ap-south-1:123456789012:function:ProcessDocumentUpload",
            "Events": ["s3:ObjectCreated:*"],
            "Filter": {
                "Key": {
                    "FilterRules": [
                        {
                            "Name": "prefix",
                            "Value": "uploads/documents/"
                        },
                        {
                            "Name": "suffix",
                            "Value": ".pdf"
                        }
                    ]
                }
            }
        }
    ],
    "QueueConfigurations": [
        {
            "Id": "BackupProcessing",
            "QueueArn": "arn:aws:sqs:ap-south-1:123456789012:backup-processing-queue",
            "Events": ["s3:ObjectCreated:*"],
            "Filter": {
                "Key": {
                    "FilterRules": [
                        {
                            "Name": "prefix",
                            "Value": "backups/"
                        }
                    ]
                }
            }
        }
    ]
}
```

## Chapter 28: S3 Cross-Region Replication (September 2022)

### The Global Expansion Challenge
With users in 15 countries, MyLearning.com faced content delivery challenges:

```
Global Content Delivery Challenges:
├── Performance Issues
│   ├── High latency for distant users
│   ├── Slow video streaming in remote regions
│   ├── Poor user experience during peak hours
│   ├── Bandwidth limitations in some regions
│   └── Network congestion affecting downloads
├── Compliance Requirements
│   ├── EU GDPR: Data residency in EU
│   ├── India: Data localization laws
│   ├── Singapore: Financial data regulations
│   ├── UAE: Government data requirements
│   └── US: Educational content compliance
├── Disaster Recovery Needs
│   ├── Single region dependency risk
│   ├── Natural disaster impact
│   ├── Service outage protection
│   ├── Data loss prevention
│   └── Business continuity requirements
├── Cost Optimization
│   ├── Data transfer costs between regions
│   ├── Storage costs in different regions
│   ├── Request costs optimization
│   ├── Bandwidth usage optimization
│   └── Currency fluctuation impact
└── Operational Complexity
    ├── Manual content synchronization
    ├── Inconsistent content across regions
    ├── Complex deployment processes
    ├── Monitoring multiple regions
    └── Troubleshooting distributed issues
```

### Understanding Cross-Region Replication (CRR)
```
S3 Cross-Region Replication Features:
├── Replication Types
│   ├── Cross-Region Replication (CRR)
│   │   ├── Replicate to different AWS regions
│   │   ├── Compliance and disaster recovery
│   │   ├── Reduced latency for global users
│   │   └── Data sovereignty requirements
│   ├── Same-Region Replication (SRR)
│   │   ├── Replicate within same region
│   │   ├── Different accounts or buckets
│   │   ├── Compliance and governance
│   │   └── Data aggregation use cases
│   └── Bi-directional Replication
│       ├── Two-way synchronization
│       ├── Active-active scenarios
│       ├── Multi-region writes
│       └── Conflict resolution needed
├── Replication Configuration
│   ├── Source bucket configuration
│   ├── Destination bucket selection
│   ├── IAM role for replication
│   ├── Prefix and tag-based filtering
│   ├── Storage class mapping
│   ├── Encryption settings
│   └── Replication metrics and notifications
├── What Gets Replicated
│   ├── New objects after rule creation
│   ├── Object metadata and tags
│   ├── Object ACLs and permissions
│   ├── Object Lock settings
│   └── Delete markers (optional)
├── What Doesn't Get Replicated
│   ├── Existing objects (before rule)
│   ├── Objects encrypted with SSE-C
│   ├── Objects in Glacier or Deep Archive
│   ├── Lifecycle actions
│   └── Objects from other replicated buckets
└── Replication Requirements
    ├── Versioning enabled on both buckets
    ├── Appropriate IAM permissions
    ├── Different regions (for CRR)
    ├── Unique bucket names
    └── Compatible encryption settings
```

### Implementing Global Content Distribution
```json
{
    "Role": "arn:aws:iam::123456789012:role/replication-role",
    "Rules": [
        {
            "ID": "ReplicateToEurope",
            "Status": "Enabled",
            "Priority": 1,
            "Filter": {
                "Prefix": "content/"
            },
            "Destination": {
                "Bucket": "arn:aws:s3:::mylearning-content-eu-west-1",
                "StorageClass": "STANDARD_IA",
                "ReplicationTime": {
                    "Status": "Enabled",
                    "Time": {
                        "Minutes": 15
                    }
                },
                "Metrics": {
                    "Status": "Enabled",
                    "EventThreshold": {
                        "Minutes": 15
                    }
                }
            }
        },
        {
            "ID": "ReplicateToSingapore",
            "Status": "Enabled",
            "Priority": 2,
            "Filter": {
                "And": {
                    "Prefix": "content/",
                    "Tags": [
                        {
                            "Key": "Region",
                            "Value": "APAC"
                        }
                    ]
                }
            },
            "Destination": {
                "Bucket": "arn:aws:s3:::mylearning-content-ap-southeast-1",
                "StorageClass": "STANDARD",
                "EncryptionConfiguration": {
                    "ReplicaKmsKeyID": "arn:aws:kms:ap-southeast-1:123456789012:key/12345678-1234-1234-1234-123456789012"
                }
            }
        },
        {
            "ID": "ReplicateToUSEast",
            "Status": "Enabled",
            "Priority": 3,
            "Filter": {
                "Prefix": "content/global/"
            },
            "Destination": {
                "Bucket": "arn:aws:s3:::mylearning-content-us-east-1",
                "StorageClass": "STANDARD",
                "AccessControlTranslation": {
                    "Owner": "Destination"
                }
            }
        }
    ]
}
```

### Intelligent Content Routing Strategy
```python
# Lambda function for intelligent content routing
import boto3
import json
from geopy.distance import geodesic

def lambda_handler(event, context):
    """
    Route content requests to nearest S3 bucket
    Based on user location and content availability
    """
    
    # Extract user location from request
    user_location = extract_user_location(event)
    content_type = event.get('content_type', 'video')
    content_id = event.get('content_id')
    
    # Define regional buckets with their locations
    regional_buckets = {
        'ap-south-1': {
            'bucket': 'mylearning-content-mumbai',
            'location': (19.0760, 72.8777),  # Mumbai
            'regions': ['IN', 'BD', 'LK', 'NP']
        },
        'ap-southeast-1': {
            'bucket': 'mylearning-content-singapore',
            'location': (1.3521, 103.8198),  # Singapore
            'regions': ['SG', 'MY', 'TH', 'ID', 'PH']
        },
        'eu-west-1': {
            'bucket': 'mylearning-content-ireland',
            'location': (53.3498, -6.2603),  # Dublin
            'regions': ['GB', 'IE', 'FR', 'DE', 'NL', 'BE']
        },
        'us-east-1': {
            'bucket': 'mylearning-content-virginia',
            'location': (38.9072, -77.0369),  # Virginia
            'regions': ['US', 'CA', 'MX']
        },
        'me-south-1': {
            'bucket': 'mylearning-content-bahrain',
            'location': (26.0667, 50.5577),  # Bahrain
            'regions': ['AE', 'SA', 'KW', 'QA', 'BH', 'OM']
        }
    }
    
    # Find the best bucket based on location and availability
    best_bucket = find_optimal_bucket(
        user_location, 
        content_id, 
        content_type, 
        regional_buckets
    )
    
    # Generate pre-signed URL for the content
    s3_client = boto3.client('s3', region_name=best_bucket['region'])
    
    try:
        presigned_url = s3_client.generate_presigned_url(
            'get_object',
            Params={
                'Bucket': best_bucket['bucket'],
                'Key': f"{content_type}/{content_id}"
            },
            ExpiresIn=3600  # 1 hour
        )
        
        return {
            'statusCode': 200,
            'body': json.dumps({
                'content_url': presigned_url,
                'bucket_region': best_bucket['region'],
                'estimated_latency': best_bucket['latency'],
                'cache_ttl': 3600
            })
        }
        
    except Exception as e:
        # Fallback to primary bucket if regional bucket fails
        return fallback_to_primary_bucket(content_id, content_type)

def find_optimal_bucket(user_location, content_id, content_type, buckets):
    """Find the optimal bucket based on distance and availability"""
    
    s3_client = boto3.client('s3')
    best_option = None
    min_score = float('inf')
    
    for region, bucket_info in buckets.items():
        try:
            # Check if content exists in this bucket
            s3_client.head_object(
                Bucket=bucket_info['bucket'],
                Key=f"{content_type}/{content_id}"
            )
            
            # Calculate distance-based latency estimate
            distance = geodesic(user_location, bucket_info['location']).kilometers
            latency_score = distance / 1000  # Rough latency estimate
            
            # Add availability and performance factors
            performance_score = get_bucket_performance_score(region)
            total_score = latency_score + performance_score
            
            if total_score < min_score:
                min_score = total_score
                best_option = {
                    'region': region,
                    'bucket': bucket_info['bucket'],
                    'latency': latency_score,
                    'distance': distance
                }
                
        except Exception as e:
            # Content not available in this bucket, skip
            continue
    
    return best_option

def get_bucket_performance_score(region):
    """Get performance score based on historical data"""
    # This would typically query CloudWatch metrics
    # For demo purposes, using static values
    performance_scores = {
        'ap-south-1': 0.1,      # Primary region, best performance
        'ap-southeast-1': 0.2,   # Good performance
        'eu-west-1': 0.3,       # Moderate performance
        'us-east-1': 0.4,       # Higher latency for Asian users
        'me-south-1': 0.25      # Good for Middle East
    }
    
    return performance_scores.get(region, 0.5)
```

## Chapter 29: S3 Analytics and Cost Optimization (October 2022)

### Storage Analytics Implementation
```
S3 Analytics and Intelligence Features:
├── S3 Storage Class Analysis
│   ├── Access pattern analysis
│   ├── Storage class recommendations
│   ├── Cost optimization insights
│   ├── Lifecycle policy suggestions
│   └── Historical usage trends
├── S3 Inventory
│   ├── Complete object listing
│   ├── Metadata and properties
│   ├── Encryption status
│   ├── Storage class distribution
│   └── Scheduled reports
├── S3 Intelligent-Tiering
│   ├── Automatic cost optimization
│   ├── Access pattern monitoring
│   ├── Seamless tier transitions
│   ├── No retrieval fees
│   └── Detailed analytics
├── CloudWatch Metrics
│   ├── Request metrics
│   ├── Storage metrics
│   ├── Data retrieval metrics
│   ├── Error rate monitoring
│   └── Custom dashboards
├── Cost and Usage Reports
│   ├── Detailed billing analysis
│   ├── Resource-level costs
│   ├── Usage patterns
│   ├── Trend analysis
│   └── Budget alerts
└── Third-party Tools Integration
    ├── CloudHealth for cost management
    ├── Datadog for monitoring
    ├── New Relic for performance
    ├── Custom analytics solutions
    └── Business intelligence tools
```

### Real-World Cost Optimization Results
```
MyLearning.com Cost Optimization Journey:
├── Initial State (Before Optimization)
│   ├── Total Storage: 100TB
│   ├── Monthly Cost: ₹2,50,000
│   ├── Storage Classes: 90% Standard, 10% IA
│   ├── Data Transfer: ₹50,000/month
│   └── Request Costs: ₹15,000/month
├── After Analytics Implementation
│   ├── Storage Class Distribution
│   │   ├── S3 Standard: 30TB (30%) - ₹69,000
│   │   ├── S3 Standard-IA: 40TB (40%) - ₹50,000
│   │   ├── S3 Glacier Instant: 20TB (20%) - ₹8,000
│   │   ├── S3 Glacier Flexible: 8TB (8%) - ₹2,880
│   │   └── S3 Deep Archive: 2TB (2%) - ₹198
│   ├── Total Storage Cost: ₹1,30,078 (48% reduction)
│   ├── Data Transfer Optimization: ₹25,000 (50% reduction)
│   ├── Request Optimization: ₹8,000 (47% reduction)
│   └── Total Monthly Cost: ₹1,63,078 (35% overall reduction)
├── Intelligent-Tiering Results
│   ├── Automatic transitions: 15TB moved to IA
│   ├── Archive transitions: 5TB moved to Glacier
│   ├── Cost savings: Additional 15% reduction
│   ├── Zero operational overhead
│   └── Improved access pattern visibility
├── Lifecycle Policy Impact
│   ├── Old versions cleanup: 20TB freed
│   ├── Incomplete uploads cleanup: 2TB freed
│   ├── Automated archival: 10TB to Deep Archive
│   ├── Cost reduction: ₹30,000/month
│   └── Storage efficiency: 22% improvement
└── Annual Savings Summary
    ├── Before optimization: ₹30,00,000/year
    ├── After optimization: ₹19,56,936/year
    ├── Total savings: ₹10,43,064/year (35%)
    ├── ROI on optimization effort: 2000%
    └── Payback period: 2 weeks
```

### Advanced Monitoring Dashboard
```python
# CloudWatch dashboard for S3 analytics
import boto3
import json

def create_s3_analytics_dashboard():
    """Create comprehensive S3 monitoring dashboard"""
    
    cloudwatch = boto3.client('cloudwatch')
    
    dashboard_body = {
        "widgets": [
            {
                "type": "metric",
                "x": 0, "y": 0,
                "width": 12, "height": 6,
                "properties": {
                    "metrics": [
                        ["AWS/S3", "BucketSizeBytes", "BucketName", "mylearning-content-prod", "StorageType", "StandardStorage"],
                        [".", ".", ".", ".", ".", "StandardIAStorage"],
                        [".", ".", ".", ".", ".", "GlacierInstantRetrievalStorage"],
                        [".", ".", ".", ".", ".", "GlacierStorage"],
                        [".", ".", ".", ".", ".", "DeepArchiveStorage"]
                    ],
                    "view": "timeSeries",
                    "stacked": True,
                    "region": "ap-south-1",
                    "title": "Storage Usage by Class",
                    "period": 86400,
                    "stat": "Average"
                }
            },
            {
                "type": "metric",
                "x": 12, "y": 0,
                "width": 12, "height": 6,
                "properties": {
                    "metrics": [
                        ["AWS/S3", "NumberOfObjects", "BucketName", "mylearning-content-prod", "StorageType", "AllStorageTypes"]
                    ],
                    "view": "timeSeries",
                    "region": "ap-south-1",
                    "title": "Total Object Count",
                    "period": 86400,
                    "stat": "Average"
                }
            },
            {
                "type": "metric",
                "x": 0, "y": 6,
                "width": 8, "height": 6,
                "properties": {
                    "metrics": [
                        ["AWS/S3", "AllRequests", "BucketName", "mylearning-content-prod"],
                        [".", "GetRequests", ".", "."],
                        [".", "PutRequests", ".", "."],
                        [".", "DeleteRequests", ".", "."]
                    ],
                    "view": "timeSeries",
                    "region": "ap-south-1",
                    "title": "Request Metrics",
                    "period": 300,
                    "stat": "Sum"
                }
            },
            {
                "type": "metric",
                "x": 8, "y": 6,
                "width": 8, "height": 6,
                "properties": {
                    "metrics": [
                        ["AWS/S3", "4xxErrors", "BucketName", "mylearning-content-prod"],
                        [".", "5xxErrors", ".", "."]
                    ],
                    "view": "timeSeries",
                    "region": "ap-south-1",
                    "title": "Error Rates",
                    "period": 300,
                    "stat": "Sum"
                }
            },
            {
                "type": "metric",
                "x": 16, "y": 6,
                "width": 8, "height": 6,
                "properties": {
                    "metrics": [
                        ["AWS/S3", "BytesDownloaded", "BucketName", "mylearning-content-prod"],
                        [".", "BytesUploaded", ".", "."]
                    ],
                    "view": "timeSeries",
                    "region": "ap-south-1",
                    "title": "Data Transfer",
                    "period": 3600,
                    "stat": "Sum"
                }
            }
        ]
    }
    
    response = cloudwatch.put_dashboard(
        DashboardName='MyLearning-S3-Analytics',
        DashboardBody=json.dumps(dashboard_body)
    )
    
    return response

# Cost analysis and alerting
def setup_cost_monitoring():
    """Set up cost monitoring and alerts"""
    
    budgets = boto3.client('budgets')
    
    # Create budget for S3 costs
    budget_definition = {
        'BudgetName': 'MyLearning-S3-Monthly-Budget',
        'BudgetLimit': {
            'Amount': '200000',  # ₹2,00,000
            'Unit': 'INR'
        },
        'TimeUnit': 'MONTHLY',
        'BudgetType': 'COST',
        'CostFilters': {
            'Service': ['Amazon Simple Storage Service']
        }
    }
    
    # Create alert when 80% of budget is reached
    notification = {
        'Notification': {
            'NotificationType': 'ACTUAL',
            'ComparisonOperator': 'GREATER_THAN',
            'Threshold': 80,
            'ThresholdType': 'PERCENTAGE'
        },
        'Subscribers': [
            {
                'SubscriptionType': 'EMAIL',
                'Address': 'finance@mylearning.com'
            },
            {
                'SubscriptionType': 'SNS',
                'Address': 'arn:aws:sns:ap-south-1:123456789012:cost-alerts'
            }
        ]
    }
    
    budgets.create_budget(
        AccountId='123456789012',
        Budget=budget_definition,
        NotificationsWithSubscribers=[notification]
    )
```

---

*The advanced S3 features implementation transformed MyLearning.com's content management and global delivery capabilities. The team had successfully built a scalable, cost-effective, and globally distributed content platform. In the final chapter of this module, we'll explore best practices, troubleshooting, and future considerations for S3 implementations.*