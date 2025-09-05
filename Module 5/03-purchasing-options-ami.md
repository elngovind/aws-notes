# EC2 Purchasing Options and AMI Deep Dive

## Chapter 37: The Cost Optimization Discovery (May 2023)

### The Billing Shock
After running their first EC2 instances for a month, Amit received the AWS bill:

*"₹18,100 for compute? That's still 60% less than our old hosting, but I wonder if we can optimize this further..."* - Amit (CEO)

During a cost optimization webinar, the team discovered that AWS offers multiple ways to purchase EC2 capacity, each with different cost implications:

```
EC2 Purchasing Options Overview:
├── On-Demand Instances (Pay-as-you-go)
│   ├── Pricing Model: Pay per hour/second of usage
│   ├── Commitment: No long-term commitment required
│   ├── Flexibility: Start/stop instances anytime
│   ├── Use Cases: Unpredictable workloads, testing, short-term projects
│   ├── Cost: Highest per-hour rate
│   ├── Availability: Always available
│   └── Best For: Development, testing, variable workloads
├── Reserved Instances (1-3 year commitment)
│   ├── Pricing Model: Upfront payment + reduced hourly rate
│   ├── Commitment: 1 or 3 year terms
│   ├── Flexibility: Regional or Availability Zone specific
│   ├── Use Cases: Steady-state workloads, predictable usage
│   ├── Cost: Up to 75% savings vs On-Demand
│   ├── Availability: Capacity reservation included
│   └── Best For: Production workloads, baseline capacity
├── Spot Instances (Bid for unused capacity)
│   ├── Pricing Model: Market-based pricing (bid system)
│   ├── Commitment: No commitment, can be terminated anytime
│   ├── Flexibility: High flexibility but with interruption risk
│   ├── Use Cases: Fault-tolerant, flexible workloads
│   ├── Cost: Up to 90% savings vs On-Demand
│   ├── Availability: Subject to capacity availability
│   └── Best For: Batch processing, CI/CD, development
├── Dedicated Hosts (Physical server dedication)
│   ├── Pricing Model: Pay for entire physical server
│   ├── Commitment: On-Demand or Reserved pricing
│   ├── Flexibility: Full control over instance placement
│   ├── Use Cases: Compliance, licensing requirements
│   ├── Cost: Highest cost but predictable
│   ├── Availability: Guaranteed physical isolation
│   └── Best For: Regulatory compliance, bring-your-own-license
├── Dedicated Instances (Isolated instances)
│   ├── Pricing Model: On-Demand + dedicated instance fee
│   ├── Commitment: No commitment required
│   ├── Flexibility: Instances run on dedicated hardware
│   ├── Use Cases: Compliance without full host control
│   ├── Cost: Higher than regular instances
│   ├── Availability: Hardware isolation guaranteed
│   └── Best For: Compliance with less control needs
└── Savings Plans (Flexible commitment)
    ├── Pricing Model: Hourly commitment for 1-3 years
    ├── Commitment: Dollar amount per hour commitment
    ├── Flexibility: Apply to any instance family/region
    ├── Use Cases: Mixed workloads, changing requirements
    ├── Cost: Up to 72% savings vs On-Demand
    ├── Availability: Flexible across services
    └── Best For: Dynamic environments, multi-service usage
```

### Deep Dive: On-Demand Instances
```
On-Demand Instances Analysis:
├── Pricing Structure
│   ├── Linux/Unix: Per-second billing (minimum 60 seconds)
│   ├── Windows: Per-hour billing
│   ├── No upfront costs or minimum fees
│   ├── Pay only for compute time used
│   └── Pricing varies by region and instance type
├── MyLearning.com Current Usage
│   ├── m5.large (Production): ₹6,048/month
│   ├── r5.large (Database): ₹8,467/month
│   ├── t3.medium (Load Balancer): ₹2,419/month
│   ├── t3.small (Development): ₹1,209/month
│   └── Total: ₹18,143/month
├── Advantages
│   ├── No upfront investment required
│   ├── Complete flexibility to start/stop
│   ├── No capacity planning needed
│   ├── Perfect for unpredictable workloads
│   ├── Ideal for testing and development
│   └── No risk of unused capacity
├── Disadvantages
│   ├── Highest per-hour cost
│   ├── No capacity guarantee during high demand
│   ├── Costs can be unpredictable
│   ├── No volume discounts
│   └── Not cost-effective for steady workloads
└── Best Use Cases for MyLearning.com
    ├── Development and testing environments
    ├── Proof of concept projects
    ├── Temporary capacity during traffic spikes
    ├── New feature testing
    └── Disaster recovery instances
```

### Deep Dive: Reserved Instances
```
Reserved Instances Deep Analysis:
├── Commitment Options
│   ├── 1-Year Term
│   │   ├── No Upfront: 0% upfront, reduced hourly rate
│   │   ├── Partial Upfront: ~50% upfront, lower hourly rate
│   │   ├── All Upfront: 100% upfront, lowest total cost
│   │   └── Savings: 20-40% vs On-Demand
│   ├── 3-Year Term
│   │   ├── No Upfront: 0% upfront, significant hourly reduction
│   │   ├── Partial Upfront: ~50% upfront, better hourly rate
│   │   ├── All Upfront: 100% upfront, maximum savings
│   │   └── Savings: 40-75% vs On-Demand
│   └── Convertible vs Standard
│       ├── Standard: Cannot change instance attributes
│       ├── Convertible: Can change instance family, OS, tenancy
│       ├── Standard: Higher discount rates
│       └── Convertible: More flexibility, slightly lower discounts
├── MyLearning.com Reserved Instance Analysis
│   ├── Production Workload (m5.large)
│   │   ├── On-Demand Cost: ₹6,048/month
│   │   ├── 1-Year Standard (All Upfront): ₹4,234/month (30% savings)
│   │   ├── 3-Year Standard (All Upfront): ₹3,024/month (50% savings)
│   │   ├── Recommendation: 1-Year Standard for flexibility
│   │   └── Annual Savings: ₹21,768
│   ├── Database Workload (r5.large)
│   │   ├── On-Demand Cost: ₹8,467/month
│   │   ├── 1-Year Standard (All Upfront): ₹5,927/month (30% savings)
│   │   ├── 3-Year Standard (All Upfront): ₹4,234/month (50% savings)
│   │   ├── Recommendation: 3-Year Standard (stable workload)
│   │   └── Annual Savings: ₹50,796
│   └── Total Potential Savings: ₹72,564/year (33% reduction)
├── Flexibility Features
│   ├── Instance Size Flexibility
│   │   ├── Apply to different sizes in same family
│   │   ├── Example: r5.large RI can cover 2x r5.medium
│   │   ├── Automatic application within family
│   │   └── No manual intervention required
│   ├── Availability Zone Flexibility
│   │   ├── Regional RIs apply to any AZ in region
│   │   ├── AZ-specific RIs provide capacity reservation
│   │   ├── Regional RIs offer more flexibility
│   │   └── AZ-specific RIs guarantee capacity
│   └── Operating System Flexibility
│       ├── Linux RIs can cover SUSE and RHEL (with normalization)
│       ├── Windows RIs are Windows-specific
│       ├── Normalization factors apply
│       └── Automatic application based on usage
├── Risk Considerations
│   ├── Commitment Risk: Locked into payment regardless of usage
│   ├── Technology Risk: Instance types may become obsolete
│   ├── Business Risk: Company growth may change requirements
│   ├── Capacity Risk: May not have enough or too much capacity
│   └── Mitigation: Start with convertible RIs for flexibility
└── Implementation Strategy for MyLearning.com
    ├── Phase 1: Purchase RIs for stable production workloads
    ├── Phase 2: Monitor usage patterns for 3 months
    ├── Phase 3: Optimize RI portfolio based on actual usage
    ├── Phase 4: Consider 3-year terms for proven stable workloads
    └── Phase 5: Regular quarterly reviews and adjustments
```

### Deep Dive: Spot Instances
```
Spot Instances Detailed Analysis:
├── How Spot Pricing Works
│   ├── AWS sells unused EC2 capacity at discounted rates
│   ├── Prices fluctuate based on supply and demand
│   ├── You set maximum price you're willing to pay
│   ├── Instance runs while spot price < your max price
│   ├── Instance terminated when spot price > your max price
│   └── 2-minute warning before termination
├── Pricing Benefits
│   ├── Typical savings: 50-90% vs On-Demand
│   ├── Historical average: 70% savings
│   ├── Price varies by instance type and AZ
│   ├── Some instance types more stable than others
│   └── Newer generation instances often cheaper
├── MyLearning.com Spot Instance Opportunities
│   ├── Development Environment
│   │   ├── Current: t3.small On-Demand (₹1,209/month)
│   │   ├── Spot Price: ~₹300/month (75% savings)
│   │   ├── Risk: Low (development can handle interruptions)
│   │   ├── Mitigation: Auto-restart, save work frequently
│   │   └── Annual Savings: ₹10,908
│   ├── Batch Processing (Video Transcoding)
│   │   ├── Workload: Convert uploaded videos to multiple formats
│   │   ├── Current: On-Demand c5.large when needed
│   │   ├── Spot Opportunity: c5.large at 80% discount
│   │   ├── Risk: Medium (can retry failed jobs)
│   │   ├── Mitigation: Checkpoint progress, queue-based processing
│   │   └── Estimated Savings: ₹15,000/month
│   ├── Testing and CI/CD
│   │   ├── Workload: Automated testing, builds
│   │   ├── Current: On-Demand instances for builds
│   │   ├── Spot Opportunity: Various instance types
│   │   ├── Risk: Low (can retry builds)
│   │   ├── Mitigation: Multiple AZs, flexible instance types
│   │   └── Estimated Savings: ₹8,000/month
│   └── Analytics Processing
│       ├── Workload: User behavior analysis, reporting
│       ├── Current: Manual analysis on production servers
│       ├── Spot Opportunity: r5.xlarge for data processing
│       ├── Risk: Low (batch processing, not time-critical)
│       ├── Mitigation: Save intermediate results
│       └── Estimated Savings: ₹12,000/month
├── Best Practices for Spot Instances
│   ├── Application Design
│   │   ├── Design for interruption tolerance
│   │   ├── Implement checkpointing and state saving
│   │   ├── Use queue-based architectures
│   │   ├── Distribute workload across multiple instances
│   │   └── Implement graceful shutdown handling
│   ├── Instance Management
│   │   ├── Diversify across instance types and AZs
│   │   ├── Use Spot Fleet for automatic diversification
│   │   ├── Monitor spot price trends
│   │   ├── Set reasonable maximum prices
│   │   └── Use mixed instance types in Auto Scaling Groups
│   ├── Cost Optimization
│   │   ├── Analyze historical spot prices
│   │   ├── Choose less popular instance types
│   │   ├── Use newer generation instances
│   │   ├── Consider different regions for better prices
│   │   └── Implement automated bidding strategies
│   └── Risk Mitigation
│       ├── Combine with On-Demand for critical workloads
│       ├── Use multiple AZs for redundancy
│       ├── Implement automatic failover mechanisms
│       ├── Monitor interruption rates by instance type
│       └── Have backup plans for capacity shortages
└── Implementation Roadmap
    ├── Week 1: Move development environment to Spot
    ├── Week 2: Implement video processing on Spot instances
    ├── Week 3: Set up CI/CD pipeline with Spot instances
    ├── Week 4: Deploy analytics processing on Spot
    └── Week 5: Monitor and optimize spot usage patterns
```

## Chapter 38: Understanding Amazon Machine Images (AMI) (May 2023)

### The Template Discovery
*"Every time we launch an EC2 instance, we need to choose an AMI. But what exactly is an AMI, and how do we pick the right one?"* - Raj

```
Amazon Machine Image (AMI) Fundamentals:
├── What is an AMI?
│   ├── Pre-configured template for EC2 instances
│   ├── Contains operating system and software
│   ├── Includes configuration settings
│   ├── Defines root volume and additional storage
│   ├── Specifies security and access permissions
│   └── Acts as a "snapshot" of a configured system
├── AMI Components
│   ├── Root Volume Template
│   │   ├── Operating system files
│   │   ├── Application software
│   │   ├── Configuration files
│   │   ├── System settings
│   │   └── User data and scripts
│   ├── Launch Permissions
│   │   ├── Public: Available to all AWS accounts
│   │   ├── Private: Available only to your account
│   │   ├── Shared: Available to specific AWS accounts
│   │   └── Marketplace: Commercial AMIs with licensing
│   ├── Block Device Mapping
│   │   ├── Root device type (EBS or Instance Store)
│   │   ├── Additional EBS volumes
│   │   ├── Instance store volumes
│   │   ├── Volume sizes and types
│   │   └── Encryption settings
│   └── Metadata
│       ├── AMI ID (unique identifier)
│       ├── Name and description
│       ├── Architecture (x86_64, arm64)
│       ├── Virtualization type (HVM, PV)
│       └── Creation date and owner
├── Types of AMIs
│   ├── AWS-Provided AMIs
│   │   ├── Amazon Linux 2
│   │   ├── Ubuntu Server
│   │   ├── Windows Server
│   │   ├── Red Hat Enterprise Linux
│   │   ├── SUSE Linux Enterprise Server
│   │   └── Regularly updated and maintained by AWS
│   ├── AWS Marketplace AMIs
│   │   ├── Third-party software vendors
│   │   ├── Pre-configured applications
│   │   ├── Commercial licensing included
│   │   ├── Support from software vendors
│   │   └── Additional hourly charges may apply
│   ├── Community AMIs
│   │   ├── Created and shared by AWS community
│   │   ├── Free to use (usually)
│   │   ├── No official support
│   │   ├── Varying quality and security
│   │   └── Use with caution in production
│   └── Custom AMIs
│       ├── Created from your configured instances
│       ├── Contains your specific software and settings
│       ├── Private to your account (unless shared)
│       ├── Faster deployment of standardized environments
│       └── Version control for your infrastructure
└── AMI Lifecycle
    ├── Creation: From running instance or snapshot
    ├── Registration: Make available for launching instances
    ├── Sharing: Grant permissions to other accounts
    ├── Copying: Duplicate across regions
    ├── Deprecation: Mark as outdated
    └── Deregistration: Remove from availability
```

### MyLearning.com's AMI Strategy
```
AMI Selection and Management Strategy:
├── Base AMI Selection Criteria
│   ├── Operating System Compatibility
│   │   ├── Must support Python 3.8+ and Django
│   │   ├── PostgreSQL client libraries required
│   │   ├── Redis client support needed
│   │   ├── SSL/TLS libraries for HTTPS
│   │   └── Git and development tools
│   ├── Security Requirements
│   │   ├── Regular security updates available
│   │   ├── Minimal attack surface
│   │   ├── No unnecessary services running
│   │   ├── Secure default configurations
│   │   └── Compliance with security standards
│   ├── Performance Considerations
│   │   ├── Optimized for EC2 environment
│   │   ├── Fast boot times
│   │   ├── Efficient resource utilization
│   │   ├── Network performance optimizations
│   │   └── Storage performance tuning
│   ├── Support and Maintenance
│   │   ├── Long-term support (LTS) versions
│   │   ├── Regular updates and patches
│   │   ├── Community or vendor support
│   │   ├── Documentation availability
│   │   └── Predictable release cycles
│   └── Cost Considerations
│       ├── No additional licensing fees
│       ├── Efficient resource usage
│       ├── Compatibility with Reserved Instances
│       ├── No vendor lock-in
│       └── Open-source preferred
├── Selected Base AMIs
│   ├── Production Web Servers
│   │   ├── AMI: Amazon Linux 2 (ami-0abcdef1234567890)
│   │   ├── Reasoning: AWS-optimized, free, secure, well-supported
│   │   ├── Instance Types: m5.large, m5.xlarge
│   │   ├── Updates: Monthly security patches
│   │   └── Backup Strategy: Custom AMI creation monthly
│   ├── Database Servers
│   │   ├── AMI: Amazon Linux 2 (ami-0abcdef1234567890)
│   │   ├── Reasoning: Consistent with web servers, PostgreSQL compatibility
│   │   ├── Instance Types: r5.large, r5.xlarge
│   │   ├── Updates: Coordinated with application updates
│   │   └── Backup Strategy: Custom AMI + EBS snapshots
│   ├── Development Environment
│   │   ├── AMI: Ubuntu 20.04 LTS (ami-0fedcba0987654321)
│   │   ├── Reasoning: Developer familiarity, extensive packages
│   │   ├── Instance Types: t3.small, t3.medium
│   │   ├── Updates: Weekly updates during development
│   │   └── Backup Strategy: Code in Git, disposable instances
│   └── Load Balancer/Cache
│       ├── AMI: Amazon Linux 2 (ami-0abcdef1234567890)
│       ├── Reasoning: Lightweight, Nginx compatibility
│       ├── Instance Types: t3.medium
│       ├── Updates: Quarterly updates
│       └── Backup Strategy: Configuration in version control
├── Custom AMI Creation Process
│   ├── Step 1: Launch Base AMI
│   │   ├── Choose appropriate base AMI
│   │   ├── Select correct instance type
│   │   ├── Configure security groups
│   │   ├── Launch in appropriate subnet
│   │   └── Connect via SSH/RDP
│   ├── Step 2: Install and Configure Software
│   │   ├── Update operating system packages
│   │   ├── Install application dependencies
│   │   ├── Configure application settings
│   │   ├── Set up monitoring agents
│   │   ├── Configure security settings
│   │   └── Install custom applications
│   ├── Step 3: System Hardening
│   │   ├── Remove unnecessary packages
│   │   ├── Disable unused services
│   │   ├── Configure firewall rules
│   │   ├── Set up log rotation
│   │   ├── Configure automatic updates
│   │   └── Apply security patches
│   ├── Step 4: Testing and Validation
│   │   ├── Test application functionality
│   │   ├── Verify security configurations
│   │   ├── Check performance benchmarks
│   │   ├── Validate monitoring setup
│   │   └── Document configuration changes
│   ├── Step 5: Create Custom AMI
│   │   ├── Stop the instance (for EBS-backed)
│   │   ├── Create AMI from instance
│   │   ├── Add descriptive name and tags
│   │   ├── Set appropriate permissions
│   │   └── Document AMI details
│   └── Step 6: Version Management
│       ├── Tag AMIs with version numbers
│       ├── Maintain AMI changelog
│       ├── Test new AMIs in staging
│       ├── Gradually roll out to production
│       └── Deregister old AMIs after validation
└── AMI Management Best Practices
    ├── Naming Convention
    │   ├── Format: mylearning-[role]-[version]-[date]
    │   ├── Example: mylearning-webserver-v1.2-20230515
    │   ├── Include environment in name if needed
    │   ├── Use consistent versioning scheme
    │   └── Add descriptive tags for searchability
    ├── Security Management
    │   ├── Regular security scanning of AMIs
    │   ├── Patch management process
    │   ├── Remove sensitive data before AMI creation
    │   ├── Encrypt AMIs containing sensitive data
    │   └── Regular security audits
    ├── Cost Optimization
    │   ├── Delete unused AMIs regularly
    │   ├── Clean up associated snapshots
    │   ├── Use lifecycle policies for automatic cleanup
    │   ├── Monitor AMI storage costs
    │   └── Share AMIs across accounts when appropriate
    ├── Automation
    │   ├── Automated AMI creation pipelines
    │   ├── Scheduled AMI updates
    │   ├── Automated testing of new AMIs
    │   ├── Integration with CI/CD pipelines
    │   └── Automated compliance checking
    └── Documentation
        ├── Maintain AMI inventory
        ├── Document software versions
        ├── Record configuration changes
        ├── Track security patches applied
        └── Maintain rollback procedures
```

---

*With a solid understanding of purchasing options and AMIs, the team was finally ready to launch their first production EC2 instance. But first, they needed to understand how to actually access and manage these virtual machines once they were running...*