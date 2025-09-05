# AWS Account Setup & Free Tier

## MyLearning.com's Account Creation Journey

### The Decision Day (February 1, 2021)
After weeks of research, the MyLearning.com team was ready to create their first AWS account. Amit, as the CEO, would be the account owner, but they had learned from their research that they needed a proper strategy.

```
Pre-Setup Planning Checklist:
├── Business Information
│   ├── Company name: MyLearning.com Pvt Ltd
│   ├── Business address: Bangalore, India
│   ├── Tax identification: GST number
│   └── Contact information: Official email
├── Payment Method
│   ├── Corporate credit card
│   ├── Backup payment method
│   ├── Billing address verification
│   └── Currency preference (INR)
├── Security Preparation
│   ├── Strong root password
│   ├── MFA device ready
│   ├── Secure email access
│   └── Recovery information
└── Initial Planning
    ├── Account naming convention
    ├── Region selection strategy
    ├── Service usage estimates
    └── Budget planning
```

## AWS Account Types

### 1. Personal Account
**MyLearning.com's Initial Choice**: Started with personal account for learning

```
Personal Account Features:
├── Individual ownership
├── Personal credit card billing
├── Basic support included
├── All AWS services available
├── Free Tier benefits
└── Easy to upgrade later
```

### 2. Business Account
**MyLearning.com's Upgrade**: Moved to business account after 3 months

```
Business Account Benefits:
├── Company billing information
├── Tax-exempt status (if applicable)
├── Business support options
├── Multiple payment methods
├── Detailed billing reports
├── Cost allocation tags
└── Enterprise features access
```

### 3. AWS Organizations Account
**MyLearning.com's Current Setup**: Master account with multiple sub-accounts

```
Organizations Structure:
├── Master Account (Billing)
├── Production Account
├── Staging Account
├── Development Account
├── Security Account
└── Analytics Account
```

## AWS Account Creation Process

### Step-by-Step Account Setup

#### Step 1: Initial Registration
```
Account Creation Form:
├── Email Address
│   ├── Use company email
│   ├── Avoid personal emails
│   ├── Ensure access continuity
│   └── Enable email notifications
├── Password Requirements
│   ├── Minimum 8 characters
│   ├── Mix of letters, numbers, symbols
│   ├── Avoid common passwords
│   └── Use password manager
├── AWS Account Name
│   ├── Company or project name
│   ├── Descriptive and clear
│   ├── Avoid special characters
│   └── Consider future scaling
└── Contact Information
    ├── Full legal name
    ├── Phone number with country code
    ├── Complete address
    └── Accurate information for billing
```

#### Step 2: Contact Information
```
MyLearning.com Contact Details:
├── Account Type: Business
├── Company Name: MyLearning.com Pvt Ltd
├── Country: India
├── Address: 
│   ├── Street: 123 Tech Park Road
│   ├── City: Bangalore
│   ├── State: Karnataka
│   ├── Postal Code: 560001
│   └── Country: India
├── Phone: +91-80-12345678
└── Tax Registration: GST Number
```

#### Step 3: Payment Information
```
Payment Method Setup:
├── Credit/Debit Card
│   ├── Card number
│   ├── Expiration date
│   ├── CVV code
│   └── Cardholder name
├── Billing Address
│   ├── Same as contact address
│   ├── Or separate billing address
│   ├── Verify with bank
│   └── Currency selection
├── Verification Process
│   ├── Small charge ($1-2)
│   ├── Temporary authorization
│   ├── Refunded automatically
│   └── Confirms card validity
└── Backup Payment Method
    ├── Add secondary card
    ├── Bank account (where available)
    ├── Prevent service interruption
    └── Automatic failover
```

#### Step 4: Identity Verification
```
Verification Methods:
├── Phone Verification
│   ├── Automated call
│   ├── PIN code entry
│   ├── SMS verification (backup)
│   └── Multiple attempts allowed
├── Required Information
│   ├── Phone number
│   ├── Country code
│   ├── PIN display on screen
│   └── Enter PIN when prompted
├── Common Issues
│   ├── VoIP numbers may not work
│   ├── International calling issues
│   ├── Network connectivity problems
│   └── Alternative verification methods
└── Troubleshooting
    ├── Try different phone number
    ├── Check network connection
    ├── Contact AWS support
    └── Use SMS verification
```

#### Step 5: Support Plan Selection
```
Support Plan Options:
├── Basic Support (Free)
│   ├── Customer service for billing
│   ├── Service health dashboard
│   ├── Documentation and forums
│   └── AWS Trusted Advisor (limited)
├── Developer Support ($29/month)
│   ├── Business hours email support
│   ├── General guidance
│   ├── System impaired response
│   └── Full Trusted Advisor
├── Business Support ($100/month)
│   ├── 24/7 phone and email support
│   ├── Production system impaired
│   ├── Infrastructure event management
│   └── API support
└── Enterprise Support ($15,000/month)
    ├── Dedicated Technical Account Manager
    ├── Concierge support team
    ├── Infrastructure event management
    └── Well-Architected reviews
```

**MyLearning.com's Choice**: Started with Basic, upgraded to Developer after 6 months, then Business after reaching production scale.

## AWS Free Tier Deep Dive

### Free Tier Categories

#### 1. Always Free
```
Always Free Services (No Expiration):
├── AWS Lambda
│   ├── 1 million requests per month
│   ├── 400,000 GB-seconds compute time
│   ├── Perfect for serverless functions
│   └── MyLearning.com use: Image processing
├── Amazon DynamoDB
│   ├── 25 GB storage
│   ├── 25 read/write capacity units
│   ├── NoSQL database service
│   └── MyLearning.com use: User sessions
├── Amazon SNS
│   ├── 1 million publishes
│   ├── 100,000 HTTP/HTTPS deliveries
│   ├── 1,000 email deliveries
│   └── MyLearning.com use: Notifications
├── Amazon SQS
│   ├── 1 million requests
│   ├── Message queuing service
│   ├── Decoupling applications
│   └── MyLearning.com use: Background jobs
└── Amazon CloudWatch
    ├── 10 custom metrics
    ├── 10 alarms
    ├── 1 million API requests
    └── MyLearning.com use: Basic monitoring
```

#### 2. 12 Months Free
```
12-Month Free Tier (From Account Creation):
├── Amazon EC2
│   ├── 750 hours per month
│   ├── t2.micro instances only
│   ├── Linux and Windows
│   └── MyLearning.com use: Web servers
├── Amazon S3
│   ├── 5 GB standard storage
│   ├── 20,000 GET requests
│   ├── 2,000 PUT requests
│   └── MyLearning.com use: Static assets
├── Amazon RDS
│   ├── 750 hours per month
│   ├── db.t2.micro instances
│   ├── 20 GB storage
│   └── MyLearning.com use: Database
├── Amazon CloudFront
│   ├── 50 GB data transfer out
│   ├── 2 million HTTP requests
│   ├── Global content delivery
│   └── MyLearning.com use: CDN
├── Elastic Load Balancing
│   ├── 750 hours per month
│   ├── 15 GB data processing
│   ├── Application Load Balancer
│   └── MyLearning.com use: Load balancing
└── Amazon VPC
    ├── Always free
    ├── Virtual private cloud
    ├── Networking foundation
    └── MyLearning.com use: Network isolation
```

#### 3. Trials
```
Trial Offers (Limited Time):
├── Amazon Redshift
│   ├── 2 months free
│   ├── dc2.large node
│   ├── Data warehousing
│   └── MyLearning.com use: Analytics
├── Amazon ElastiCache
│   ├── 750 hours per month
│   ├── cache.t2.micro nodes
│   ├── In-memory caching
│   └── MyLearning.com use: Session storage
├── Amazon Elasticsearch
│   ├── 750 hours per month
│   ├── t2.small.elasticsearch instance
│   ├── Search and analytics
│   └── MyLearning.com use: Course search
└── AWS CodeBuild
    ├── 100 build minutes per month
    ├── Continuous integration
    ├── Build and test code
    └── MyLearning.com use: CI/CD pipeline
```

### MyLearning.com's Free Tier Strategy

#### Month 1-3: Learning Phase
```
Free Tier Usage (Learning Phase):
├── EC2 Usage
│   ├── 2 t2.micro instances
│   ├── 500 hours total usage
│   ├── Web server + database server
│   └── Cost: $0 (within free tier)
├── S3 Usage
│   ├── 2 GB storage used
│   ├── 5,000 GET requests
│   ├── 500 PUT requests
│   └── Cost: $0 (within free tier)
├── RDS Usage
│   ├── 1 db.t2.micro instance
│   ├── 400 hours usage
│   ├── 10 GB storage
│   └── Cost: $0 (within free tier)
└── Total Monthly Cost: $0
```

#### Month 4-6: Growth Phase
```
Free Tier Usage (Growth Phase):
├── EC2 Usage
│   ├── 3 t2.micro instances
│   ├── 750 hours (at limit)
│   ├── Additional t2.small: $15/month
│   └── Total EC2 cost: $15/month
├── S3 Usage
│   ├── 8 GB storage (3 GB over limit)
│   ├── 25,000 GET requests (5K over)
│   ├── 3,000 PUT requests (1K over)
│   └── Additional S3 cost: $2/month
├── RDS Usage
│   ├── 750 hours (at limit)
│   ├── 25 GB storage (5 GB over)
│   └── Additional RDS cost: $3/month
└── Total Monthly Cost: $20/month
```

#### Month 7-12: Scaling Phase
```
Free Tier Usage (Scaling Phase):
├── EC2 Usage
│   ├── Free tier: 750 hours t2.micro
│   ├── Additional instances: $45/month
│   ├── Load balancer: Free tier
│   └── Total EC2 cost: $45/month
├── S3 Usage
│   ├── Free tier: 5 GB
│   ├── Additional 20 GB: $5/month
│   ├── CloudFront: Free tier
│   └── Total storage cost: $5/month
├── RDS Usage
│   ├── Free tier: 750 hours, 20 GB
│   ├── Additional storage: $8/month
│   ├── Backup storage: $3/month
│   └── Total database cost: $11/month
└── Total Monthly Cost: $61/month
```

### Free Tier Monitoring & Alerts

#### Setting Up Billing Alerts
```
Billing Alert Configuration:
├── CloudWatch Billing Alarms
│   ├── $0 threshold (immediate alerts)
│   ├── $10 threshold (early warning)
│   ├── $25 threshold (budget concern)
│   └── $50 threshold (urgent action)
├── Free Tier Usage Alerts
│   ├── 80% of free tier limit
│   ├── 90% of free tier limit
│   ├── 100% of free tier limit
│   └── Daily usage reports
├── Service-Specific Monitoring
│   ├── EC2 instance hours
│   ├── S3 storage and requests
│   ├── RDS instance hours
│   └── Data transfer amounts
└── Automated Actions
    ├── Email notifications
    ├── SMS alerts (critical)
    ├── Slack integration
    └── Auto-scaling policies
```

#### MyLearning.com's Monitoring Setup
```
Monitoring Implementation:
├── AWS Budgets
│   ├── $0 budget for free tier
│   ├── $100 budget for first year
│   ├── Monthly budget reviews
│   └── Forecasting enabled
├── Cost Explorer
│   ├── Daily cost tracking
│   ├── Service-wise breakdown
│   ├── Usage pattern analysis
│   └── Optimization recommendations
├── Trusted Advisor
│   ├── Cost optimization checks
│   ├── Security recommendations
│   ├── Performance insights
│   └── Fault tolerance reviews
└── Custom Dashboards
    ├── Real-time cost tracking
    ├── Usage trend analysis
    ├── Budget vs actual spending
    └── Forecasting and planning
```

## Account Security Best Practices

### Initial Security Setup
```
Security Configuration Checklist:
├── Root Account Security
│   ├── Strong password (20+ characters)
│   ├── Enable MFA immediately
│   ├── Secure password storage
│   └── Limited root account usage
├── Contact Information
│   ├── Secure email account
│   ├── Alternative contact methods
│   ├── Recovery information
│   └── Regular information updates
├── Billing Security
│   ├── Billing alerts enabled
│   ├── Payment method security
│   ├── Invoice notifications
│   └── Unusual activity monitoring
└── Access Logging
    ├── CloudTrail enabled
    ├── Config service setup
    ├── GuardDuty activation
    └── Security Hub configuration
```

### MyLearning.com's Security Incident
```
Security Breach Timeline:
├── Day 1: Account Creation
│   ├── Used simple password
│   ├── No MFA enabled
│   ├── Shared credentials via email
│   └── No monitoring setup
├── Day 15: Credential Compromise
│   ├── Phishing email attack
│   ├── Credentials stolen
│   ├── Unauthorized access gained
│   └── Resources launched
├── Day 17: Discovery
│   ├── Unusual billing alert
│   ├── $45,000 in charges
│   ├── Cryptocurrency mining
│   └── Multiple regions affected
├── Day 18: Response
│   ├── Changed all passwords
│   ├── Enabled MFA
│   ├── Terminated resources
│   └── Contacted AWS support
└── Resolution
    ├── AWS credited charges
    ├── Security review completed
    ├── Best practices implemented
    └── Team security training
```

## Account Management Best Practices

### Multi-Account Strategy
```
Account Organization:
├── Master Account
│   ├── Billing and cost management
│   ├── Organization management
│   ├── Consolidated billing
│   └── Cross-account roles
├── Production Account
│   ├── Live applications
│   ├── Customer data
│   ├── Strict access controls
│   └── High availability setup
├── Staging Account
│   ├── Pre-production testing
│   ├── Integration testing
│   ├── Performance testing
│   └── Security testing
├── Development Account
│   ├── Development environments
│   ├── Experimentation
│   ├── Learning and training
│   └── Proof of concepts
└── Security Account
    ├── Logging and monitoring
    ├── Security tools
    ├── Compliance reporting
    └── Incident response
```

### Cost Management
```
Cost Optimization Strategy:
├── Regular Reviews
│   ├── Weekly cost analysis
│   ├── Monthly budget reviews
│   ├── Quarterly optimization
│   └── Annual planning
├── Resource Optimization
│   ├── Right-sizing instances
│   ├── Reserved instance planning
│   ├── Spot instance usage
│   └── Auto-scaling implementation
├── Monitoring Tools
│   ├── AWS Cost Explorer
│   ├── AWS Budgets
│   ├── Third-party tools
│   └── Custom dashboards
└── Governance
    ├── Tagging strategy
    ├── Cost allocation
    ├── Budget approvals
    └── Spending policies
```

---

*Next: Deep dive into AWS Identity and Access Management (IAM) - the foundation of AWS security that MyLearning.com learned the hard way.*