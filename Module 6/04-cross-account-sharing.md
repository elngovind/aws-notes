# Cross-Account AMI Sharing and Advanced Collaboration

## Chapter 44: The Partnership Opportunity (September 2023)

### An Unexpected Proposal

The video call notification popped up on Amit's screen at exactly 3:00 PM Singapore time. On the other end was Dr. Sarah Chen, the Chief Technology Officer of EduGlobal, one of Southeast Asia's largest educational technology companies. What started as a routine partnership discussion was about to evolve into something much more significant.

*"Amit, I'll be direct with you,"* Dr. Chen began, her voice carrying the confidence of someone who had built multiple successful tech companies. *"We've been watching MyLearning.com's growth, and we're impressed. We want to propose something that could benefit both our organizations significantly."*

EduGlobal had a problem that MyLearning.com was uniquely positioned to solve. They had built an extensive network of educational content and partnerships across Southeast Asia, but their technology platform was aging and couldn't scale to meet growing demand. Meanwhile, MyLearning.com had built a modern, scalable platform but lacked the regional content and partnerships that EduGlobal had cultivated over fifteen years.

*"We're proposing a technical partnership,"* Dr. Chen continued. *"EduGlobal would license your platform technology, and in return, we'd provide access to our content library and regional partnerships. But here's the interesting part – we want to run your exact platform configuration in our AWS accounts while maintaining complete data sovereignty and security isolation."*

The proposal was intriguing but technically complex. EduGlobal wanted to run identical copies of MyLearning.com's infrastructure in their own AWS accounts across multiple countries, each with different regulatory requirements. They needed the ability to rapidly deploy new features and updates while maintaining strict data isolation between regions and organizations.

*"The challenge,"* Raj realized as he joined the call, *"is that we'd need to share our AMIs, configurations, and deployment processes across multiple AWS accounts while ensuring security and compliance in each region. This is exactly the kind of cross-account collaboration that AWS was designed to support, but we've never implemented it at this scale."*

### Understanding Cross-Account Architecture

What the team discovered as they researched AWS cross-account capabilities was a sophisticated ecosystem designed specifically for complex organizational relationships. AWS had built comprehensive tools for sharing resources, managing access, and maintaining security boundaries across multiple accounts – exactly what they needed for the EduGlobal partnership.

The foundation of cross-account collaboration in AWS was the principle of "shared responsibility with maintained isolation." Organizations could share specific resources like AMIs, snapshots, and even entire infrastructure templates while maintaining complete control over their own environments and data.

```
AWS Cross-Account Sharing Architecture:
├── Account Isolation Principles
│   ├── Security Boundaries
│   │   ├── Complete IAM isolation between accounts
│   │   ├── Network isolation by default
│   │   ├── Resource access control per account
│   │   ├── Billing and cost separation
│   │   └── Compliance boundary enforcement
│   ├── Data Sovereignty
│   │   ├── Data never crosses account boundaries without explicit permission
│   │   ├── Regional data residency maintained per account
│   │   ├── Encryption keys isolated per account
│   │   ├── Audit trails maintained separately
│   │   └── Regulatory compliance per jurisdiction
│   ├── Operational Independence
│   │   ├── Independent scaling and performance
│   │   ├── Separate monitoring and alerting
│   │   ├── Individual backup and disaster recovery
│   │   ├── Account-specific configuration management
│   │   └── Independent update and deployment cycles
│   └── Financial Isolation
│       ├── Separate billing and cost allocation
│       ├── Independent budget management
│       ├── Account-specific cost optimization
│       ├── Resource usage tracking per account
│       └── Chargeback and cost center allocation
├── Resource Sharing Mechanisms
│   ├── AMI Sharing
│   │   ├── Cross-account AMI permissions
│   │   ├── Encrypted AMI sharing with KMS keys
│   │   ├── Regional AMI copying and sharing
│   │   ├── Marketplace-style AMI distribution
│   │   └── Version control and update management
│   ├── Snapshot Sharing
│   │   ├── EBS snapshot cross-account access
│   │   ├── Encrypted snapshot sharing
│   │   ├── Selective volume sharing
│   │   ├── Backup and disaster recovery sharing
│   │   └── Development environment provisioning
│   ├── Infrastructure Templates
│   │   ├── CloudFormation template sharing
│   │   ├── Terraform module distribution
│   │   ├── AWS CDK construct libraries
│   │   ├── Service Catalog product sharing
│   │   └── Configuration management templates
│   ├── Container Images
│   │   ├── ECR cross-account image sharing
│   │   ├── Container security scanning
│   │   ├── Image vulnerability management
│   │   ├── Multi-architecture image support
│   │   └── Automated image updates
│   └── Network Connectivity
│       ├── VPC peering across accounts
│       ├── Transit Gateway cross-account sharing
│       ├── Direct Connect sharing
│       ├── VPN connectivity options
│       └── Private endpoint sharing
├── Access Control and Security
│   ├── Cross-Account IAM Roles
│   │   ├── Assume role permissions
│   │   ├── Temporary credential generation
│   │   ├── Multi-factor authentication requirements
│   │   ├── Session duration controls
│   │   └── Condition-based access policies
│   ├── Resource-Based Policies
│   │   ├── S3 bucket policies for cross-account access
│   │   ├── KMS key policies for encryption sharing
│   │   ├── Lambda function resource policies
│   │   ├── SNS topic cross-account permissions
│   │   └── SQS queue cross-account access
│   ├── AWS Organizations Integration
│   │   ├── Centralized account management
│   │   ├── Service Control Policies (SCPs)
│   │   ├── Consolidated billing and cost management
│   │   ├── Organizational unit structure
│   │   └── Account creation and lifecycle management
│   └── Compliance and Governance
│       ├── Cross-account audit trails
│       ├── Centralized compliance monitoring
│       ├── Policy enforcement across accounts
│       ├── Risk assessment and management
│       └── Incident response coordination
├── Automation and Management
│   ├── Cross-Account Deployment Pipelines
│   │   ├── CI/CD across multiple accounts
│   │   ├── Automated testing in target accounts
│   │   ├── Staged deployment strategies
│   │   ├── Rollback capabilities
│   │   └── Approval workflows
│   ├── Configuration Management
│   │   ├── Centralized configuration distribution
│   │   ├── Account-specific customizations
│   │   ├── Version control and change tracking
│   │   ├── Compliance validation
│   │   └── Automated remediation
│   ├── Monitoring and Alerting
│   │   ├── Cross-account monitoring dashboards
│   │   ├── Centralized log aggregation
│   │   ├── Multi-account alerting strategies
│   │   ├── Performance correlation analysis
│   │   └── Security event correlation
│   └── Cost Management
│       ├── Cross-account cost allocation
│       ├── Shared resource cost tracking
│       ├── Chargeback automation
│       ├── Budget management across accounts
│       └── Cost optimization recommendations
└── Partnership Models
    ├── Technology Licensing
    │   ├── AMI and template licensing
    │   ├── Usage-based pricing models
    │   ├── Support and maintenance agreements
    │   ├── Update and upgrade management
    │   └── Intellectual property protection
    ├── Joint Development
    │   ├── Shared development environments
    │   ├── Collaborative testing frameworks
    │   ├── Code sharing and version control
    │   ├── Joint security and compliance
    │   └── Shared innovation initiatives
    ├── Managed Services
    │   ├── Platform-as-a-Service offerings
    │   ├── Managed infrastructure services
    │   ├── Support and operations outsourcing
    │   ├── Performance and availability SLAs
    │   └── Disaster recovery services
    └── Marketplace Distribution
        ├── AWS Marketplace AMI publishing
        ├── SaaS product distribution
        ├── Professional services marketplace
        ├── Partner solution catalogs
        └── Revenue sharing models
```

### Implementing Secure Cross-Account AMI Sharing

The technical implementation of cross-account AMI sharing for the EduGlobal partnership required careful planning and sophisticated automation. The team needed to create a system that could securely share their platform AMIs while maintaining version control, security, and compliance across multiple AWS accounts and regions.

```python
# Cross-Account AMI Sharing and Management System
import boto3
import json
from typing import Dict, List, Optional, Tuple
from datetime import datetime, timedelta
import hashlib

class CrossAccountAMIManager:
    def __init__(self, source_region: str = 'ap-south-1'):
        self.ec2 = boto3.client('ec2', region_name=source_region)
        self.source_region = source_region
        self.account_id = self._get_account_id()
        
    def create_shareable_ami(self, instance_id: str, ami_name: str, 
                           description: str, partner_accounts: List[str]) -> Dict:
        """
        Create an AMI optimized for cross-account sharing
        """
        try:
            # Step 1: Prepare instance for AMI creation
            preparation_result = self._prepare_instance_for_sharing(instance_id)
            if not preparation_result['Success']:
                return preparation_result
            
            # Step 2: Create the AMI
            ami_response = self.ec2.create_image(
                InstanceId=instance_id,
                Name=ami_name,
                Description=description,
                NoReboot=False,  # Ensure filesystem consistency
                TagSpecifications=[
                    {
                        'ResourceType': 'image',
                        'Tags': [
                            {'Key': 'Name', 'Value': ami_name},
                            {'Key': 'CreatedBy', 'Value': 'MyLearning-CrossAccount-System'},
                            {'Key': 'Purpose', 'Value': 'PartnerSharing'},
                            {'Key': 'Version', 'Value': self._generate_version_tag()},
                            {'Key': 'SourceInstance', 'Value': instance_id},
                            {'Key': 'CreationDate', 'Value': datetime.utcnow().isoformat()},
                            {'Key': 'PartnerAccounts', 'Value': ','.join(partner_accounts)},
                            {'Key': 'ShareableAMI', 'Value': 'true'}
                        ]
                    }
                ]
            )
            
            ami_id = ami_response['ImageId']
            
            # Step 3: Wait for AMI to be available
            print(f"Creating AMI {ami_id}... This may take several minutes.")
            waiter = self.ec2.get_waiter('image_available')
            waiter.wait(ImageIds=[ami_id])
            
            # Step 4: Configure AMI for sharing
            sharing_result = self._configure_ami_sharing(ami_id, partner_accounts)
            
            # Step 5: Create sharing documentation
            documentation = self._generate_ami_documentation(ami_id, instance_id)
            
            return {
                'Success': True,
                'AMI_ID': ami_id,
                'SharingConfiguration': sharing_result,
                'Documentation': documentation,
                'Message': f'Successfully created shareable AMI: {ami_id}'
            }
            
        except Exception as e:
            return {
                'Success': False,
                'Error': str(e),
                'Message': f'Failed to create shareable AMI from instance {instance_id}'
            }
    
    def _prepare_instance_for_sharing(self, instance_id: str) -> Dict:
        """
        Prepare an instance for AMI creation by removing sensitive data
        """
        try:
            ssm = boto3.client('ssm')
            
            # Execute cleanup script on the instance
            cleanup_commands = [
                # Remove SSH host keys (will be regenerated on first boot)
                'sudo rm -f /etc/ssh/ssh_host_*',
                
                # Clear bash history
                'history -c',
                'rm -f ~/.bash_history',
                
                # Remove temporary files
                'sudo rm -rf /tmp/*',
                'sudo rm -rf /var/tmp/*',
                
                # Clear log files
                'sudo truncate -s 0 /var/log/*.log',
                'sudo find /var/log -name "*.log" -exec truncate -s 0 {} \\;',
                
                # Remove user-specific data
                'sudo rm -rf /home/*/.ssh/authorized_keys',
                'sudo rm -rf /root/.ssh/authorized_keys',
                
                # Clear package manager cache
                'sudo yum clean all || sudo apt-get clean',
                
                # Remove network configuration that might be instance-specific
                'sudo rm -f /etc/udev/rules.d/70-persistent-net.rules',
                
                # Clear machine-id (will be regenerated)
                'sudo truncate -s 0 /etc/machine-id',
                
                # Sync filesystem
                'sudo sync'
            ]
            
            response = ssm.send_command(
                InstanceIds=[instance_id],
                DocumentName='AWS-RunShellScript',
                Parameters={'commands': cleanup_commands},
                Comment='Preparing instance for AMI creation and sharing'
            )
            
            command_id = response['Command']['CommandId']
            
            # Wait for command completion
            waiter = ssm.get_waiter('command_executed')
            waiter.wait(CommandId=command_id, InstanceId=instance_id)
            
            # Check command status
            result = ssm.get_command_invocation(
                CommandId=command_id,
                InstanceId=instance_id
            )
            
            if result['Status'] == 'Success':
                return {
                    'Success': True,
                    'Message': 'Instance prepared successfully for AMI creation'
                }
            else:
                return {
                    'Success': False,
                    'Error': result.get('StandardErrorContent', 'Unknown error'),
                    'Message': 'Failed to prepare instance for AMI creation'
                }
                
        except Exception as e:
            return {
                'Success': False,
                'Error': str(e),
                'Message': f'Failed to prepare instance {instance_id} for sharing'
            }
    
    def _configure_ami_sharing(self, ami_id: str, partner_accounts: List[str]) -> Dict:
        """
        Configure AMI sharing permissions and encryption
        """
        try:
            # Get AMI details to check encryption status
            ami_details = self.ec2.describe_images(ImageIds=[ami_id])['Images'][0]
            
            sharing_config = {
                'AMI_ID': ami_id,
                'SharedAccounts': [],
                'EncryptionStatus': {},
                'Snapshots': []
            }
            
            # Handle encrypted AMIs
            for block_device in ami_details['BlockDeviceMappings']:
                if 'Ebs' in block_device:
                    snapshot_id = block_device['Ebs']['SnapshotId']
                    
                    # Get snapshot details
                    snapshot_details = self.ec2.describe_snapshots(
                        SnapshotIds=[snapshot_id]
                    )['Snapshots'][0]
                    
                    sharing_config['Snapshots'].append({
                        'SnapshotId': snapshot_id,
                        'Encrypted': snapshot_details['Encrypted'],
                        'DeviceName': block_device['DeviceName']
                    })
                    
                    if snapshot_details['Encrypted']:
                        # Share encrypted snapshot with partner accounts
                        self._share_encrypted_snapshot(snapshot_id, partner_accounts)
            
            # Share AMI with partner accounts
            for account_id in partner_accounts:
                try:
                    self.ec2.modify_image_attribute(
                        ImageId=ami_id,
                        Attribute='launchPermission',
                        OperationType='add',
                        UserIds=[account_id]
                    )
                    
                    sharing_config['SharedAccounts'].append({
                        'AccountId': account_id,
                        'Status': 'Success',
                        'SharedDate': datetime.utcnow().isoformat()
                    })
                    
                except Exception as e:
                    sharing_config['SharedAccounts'].append({
                        'AccountId': account_id,
                        'Status': 'Failed',
                        'Error': str(e)
                    })
            
            return sharing_config
            
        except Exception as e:
            return {
                'Error': str(e),
                'Message': f'Failed to configure sharing for AMI {ami_id}'
            }
    
    def _share_encrypted_snapshot(self, snapshot_id: str, partner_accounts: List[str]):
        """
        Share encrypted snapshots with partner accounts
        """
        try:
            for account_id in partner_accounts:
                self.ec2.modify_snapshot_attribute(
                    SnapshotId=snapshot_id,
                    Attribute='createVolumePermission',
                    OperationType='add',
                    UserIds=[account_id]
                )
                
        except Exception as e:
            print(f"Warning: Failed to share snapshot {snapshot_id}: {e}")
    
    def copy_ami_to_partner_region(self, ami_id: str, destination_region: str, 
                                  partner_account_id: str, kms_key_id: Optional[str] = None) -> Dict:
        """
        Copy AMI to a different region for partner account access
        """
        try:
            dest_ec2 = boto3.client('ec2', region_name=destination_region)
            
            # Get source AMI details
            source_ami = self.ec2.describe_images(ImageIds=[ami_id])['Images'][0]
            
            # Copy AMI to destination region
            copy_params = {
                'SourceImageId': ami_id,
                'SourceRegion': self.source_region,
                'Name': f"{source_ami['Name']}-{destination_region}",
                'Description': f"Regional copy of {source_ami['Description']}",
                'Encrypted': True
            }
            
            if kms_key_id:
                copy_params['KmsKeyId'] = kms_key_id
            
            copy_response = dest_ec2.copy_image(**copy_params)
            copied_ami_id = copy_response['ImageId']
            
            # Wait for copy to complete
            print(f"Copying AMI to {destination_region}... This may take several minutes.")
            waiter = dest_ec2.get_waiter('image_available')
            waiter.wait(ImageIds=[copied_ami_id])
            
            # Share copied AMI with partner account
            dest_ec2.modify_image_attribute(
                ImageId=copied_ami_id,
                Attribute='launchPermission',
                OperationType='add',
                UserIds=[partner_account_id]
            )
            
            return {
                'Success': True,
                'SourceAMI': ami_id,
                'CopiedAMI': copied_ami_id,
                'DestinationRegion': destination_region,
                'SharedWithAccount': partner_account_id,
                'Message': f'Successfully copied and shared AMI in {destination_region}'
            }
            
        except Exception as e:
            return {
                'Success': False,
                'Error': str(e),
                'Message': f'Failed to copy AMI to {destination_region}'
            }
    
    def create_ami_sharing_agreement(self, ami_id: str, partner_account_id: str, 
                                   agreement_terms: Dict) -> Dict:
        """
        Create a formal AMI sharing agreement with terms and conditions
        """
        try:
            agreement = {
                'AgreementId': self._generate_agreement_id(ami_id, partner_account_id),
                'AMI_ID': ami_id,
                'PartnerAccountId': partner_account_id,
                'SharerAccountId': self.account_id,
                'CreationDate': datetime.utcnow().isoformat(),
                'Terms': agreement_terms,
                'Status': 'Active',
                'Compliance': {
                    'DataResidency': agreement_terms.get('data_residency', 'Not Specified'),
                    'EncryptionRequired': agreement_terms.get('encryption_required', True),
                    'AuditTrail': agreement_terms.get('audit_trail', True),
                    'SupportLevel': agreement_terms.get('support_level', 'Standard')
                }
            }
            
            # Store agreement in S3 for record keeping
            s3 = boto3.client('s3')
            agreement_key = f"ami-sharing-agreements/{agreement['AgreementId']}.json"
            
            s3.put_object(
                Bucket='mylearning-compliance-records',
                Key=agreement_key,
                Body=json.dumps(agreement, indent=2),
                ServerSideEncryption='aws:kms',
                Metadata={
                    'AgreementType': 'AMI-Sharing',
                    'PartnerAccount': partner_account_id,
                    'CreatedBy': 'MyLearning-CrossAccount-System'
                }
            )
            
            return {
                'Success': True,
                'Agreement': agreement,
                'StorageLocation': f's3://mylearning-compliance-records/{agreement_key}',
                'Message': 'AMI sharing agreement created successfully'
            }
            
        except Exception as e:
            return {
                'Success': False,
                'Error': str(e),
                'Message': 'Failed to create AMI sharing agreement'
            }
    
    def monitor_shared_ami_usage(self, ami_id: str) -> Dict:
        """
        Monitor usage of shared AMIs across partner accounts
        """
        try:
            # Get AMI sharing information
            ami_attributes = self.ec2.describe_image_attribute(
                ImageId=ami_id,
                Attribute='launchPermission'
            )
            
            shared_accounts = [
                perm['UserId'] for perm in ami_attributes['LaunchPermissions']
                if 'UserId' in perm
            ]
            
            usage_report = {
                'AMI_ID': ami_id,
                'SharedAccounts': shared_accounts,
                'UsageMetrics': {},
                'ComplianceStatus': {},
                'LastUpdated': datetime.utcnow().isoformat()
            }
            
            # Note: Cross-account usage monitoring requires partner cooperation
            # This would typically involve partner accounts sending usage metrics
            # to a shared monitoring system or API
            
            for account_id in shared_accounts:
                usage_report['UsageMetrics'][account_id] = {
                    'Status': 'Monitoring requires partner cooperation',
                    'RecommendedApproach': 'Implement shared monitoring API'
                }
            
            return {
                'Success': True,
                'UsageReport': usage_report,
                'Message': f'Generated usage report for AMI {ami_id}'
            }
            
        except Exception as e:
            return {
                'Success': False,
                'Error': str(e),
                'Message': f'Failed to monitor AMI usage for {ami_id}'
            }
    
    def _generate_version_tag(self) -> str:
        """Generate version tag based on current date and time"""
        return datetime.utcnow().strftime('%Y.%m.%d.%H%M')
    
    def _generate_agreement_id(self, ami_id: str, partner_account_id: str) -> str:
        """Generate unique agreement ID"""
        content = f"{ami_id}-{partner_account_id}-{datetime.utcnow().isoformat()}"
        return hashlib.md5(content.encode()).hexdigest()[:16]
    
    def _generate_ami_documentation(self, ami_id: str, source_instance_id: str) -> Dict:
        """Generate comprehensive documentation for shared AMI"""
        
        # Get AMI details
        ami_details = self.ec2.describe_images(ImageIds=[ami_id])['Images'][0]
        
        # Get source instance details
        instance_details = self.ec2.describe_instances(
            InstanceIds=[source_instance_id]
        )['Reservations'][0]['Instances'][0]
        
        documentation = {
            'AMI_Information': {
                'AMI_ID': ami_id,
                'Name': ami_details['Name'],
                'Description': ami_details['Description'],
                'Architecture': ami_details['Architecture'],
                'VirtualizationType': ami_details['VirtualizationType'],
                'RootDeviceType': ami_details['RootDeviceType'],
                'CreationDate': ami_details['CreationDate']
            },
            'Source_Configuration': {
                'InstanceId': source_instance_id,
                'InstanceType': instance_details['InstanceType'],
                'Platform': ami_details.get('Platform', 'Linux'),
                'BlockDeviceMappings': ami_details['BlockDeviceMappings']
            },
            'Software_Stack': {
                'OperatingSystem': 'Amazon Linux 2',
                'WebServer': 'Nginx 1.20+',
                'ApplicationServer': 'Gunicorn with Python 3.8',
                'Database': 'PostgreSQL Client 13+',
                'Caching': 'Redis Client 6+',
                'Monitoring': 'CloudWatch Agent',
                'Security': 'AWS Systems Manager Agent'
            },
            'Launch_Instructions': {
                'RecommendedInstanceTypes': ['t3.medium', 'm5.large', 'm5.xlarge'],
                'MinimumRequirements': {
                    'vCPUs': 2,
                    'Memory': '4 GB',
                    'Storage': '20 GB'
                },
                'SecurityGroups': {
                    'Required': ['SSH (22)', 'HTTP (80)', 'HTTPS (443)'],
                    'Optional': ['Custom Application Ports']
                },
                'PostLaunchSteps': [
                    'Update application configuration files',
                    'Configure database connections',
                    'Set up SSL certificates',
                    'Configure monitoring and logging',
                    'Run application-specific setup scripts'
                ]
            },
            'Support_Information': {
                'Documentation': 'https://docs.mylearning.com/ami-deployment',
                'SupportEmail': 'partners@mylearning.com',
                'SLA': 'Business hours support (9 AM - 6 PM IST)',
                'UpdateSchedule': 'Monthly security updates, quarterly feature updates'
            }
        }
        
        return documentation
    
    def _get_account_id(self) -> str:
        """Get current AWS account ID"""
        return boto3.client('sts').get_caller_identity()['Account']

# Implementation for EduGlobal Partnership
def implement_eduglobal_partnership():
    """Implement cross-account AMI sharing for EduGlobal partnership"""
    
    ami_manager = CrossAccountAMIManager()
    
    print("🤝 Starting EduGlobal Partnership Implementation")
    
    # EduGlobal account IDs for different regions
    eduglobal_accounts = {
        'singapore': '123456789012',
        'malaysia': '123456789013', 
        'thailand': '123456789014',
        'indonesia': '123456789015'
    }
    
    # Step 1: Create shareable AMI from production instance
    print("\n📋 Step 1: Creating Shareable AMI")
    production_instance = 'i-0123456789abcdef0'
    
    ami_result = ami_manager.create_shareable_ami(
        instance_id=production_instance,
        ami_name='MyLearning-Platform-v2023.09',
        description='MyLearning.com Platform AMI for EduGlobal Partnership',
        partner_accounts=list(eduglobal_accounts.values())
    )
    
    if ami_result['Success']:
        shared_ami_id = ami_result['AMI_ID']
        print(f"✅ Created shareable AMI: {shared_ami_id}")
        
        # Step 2: Copy AMI to partner regions
        print("\n📋 Step 2: Copying AMI to Partner Regions")
        
        regional_copies = {}
        for region, account_id in eduglobal_accounts.items():
            if region == 'singapore':
                target_region = 'ap-southeast-1'
            elif region == 'malaysia':
                target_region = 'ap-southeast-1'  # Same region as Singapore
            elif region == 'thailand':
                target_region = 'ap-southeast-1'  # Same region
            else:  # Indonesia
                target_region = 'ap-southeast-3'
            
            copy_result = ami_manager.copy_ami_to_partner_region(
                ami_id=shared_ami_id,
                destination_region=target_region,
                partner_account_id=account_id
            )
            
            if copy_result['Success']:
                regional_copies[region] = copy_result['CopiedAMI']
                print(f"✅ Copied to {region}: {copy_result['CopiedAMI']}")
            else:
                print(f"❌ Failed to copy to {region}: {copy_result['Message']}")
        
        # Step 3: Create sharing agreements
        print("\n📋 Step 3: Creating Sharing Agreements")
        
        agreement_terms = {
            'usage_type': 'Commercial License',
            'data_residency': 'Regional data must remain in specified regions',
            'encryption_required': True,
            'audit_trail': True,
            'support_level': 'Premium',
            'update_frequency': 'Monthly',
            'termination_notice': '30 days',
            'compliance_requirements': ['GDPR', 'PDPA', 'Local regulations']
        }
        
        for region, account_id in eduglobal_accounts.items():
            agreement_result = ami_manager.create_ami_sharing_agreement(
                ami_id=regional_copies.get(region, shared_ami_id),
                partner_account_id=account_id,
                agreement_terms=agreement_terms
            )
            
            if agreement_result['Success']:
                print(f"✅ Created agreement for {region}: {agreement_result['Agreement']['AgreementId']}")
            else:
                print(f"❌ Failed to create agreement for {region}")
        
        # Step 4: Set up monitoring
        print("\n📋 Step 4: Setting Up Usage Monitoring")
        
        monitoring_result = ami_manager.monitor_shared_ami_usage(shared_ami_id)
        if monitoring_result['Success']:
            print("✅ Usage monitoring configured")
            print("📊 Monitoring requires partner cooperation for detailed metrics")
        
        print(f"\n🎉 EduGlobal partnership implementation completed!")
        print(f"📋 Primary AMI: {shared_ami_id}")
        print(f"📋 Regional copies: {len(regional_copies)}")
        print(f"📋 Partner accounts: {len(eduglobal_accounts)}")
        
    else:
        print(f"❌ Failed to create shareable AMI: {ami_result['Message']}")

if __name__ == "__main__":
    implement_eduglobal_partnership()
```

### The Partnership Success Story

The implementation of cross-account AMI sharing for the EduGlobal partnership exceeded everyone's expectations. Within six weeks of the technical implementation, EduGlobal had successfully deployed MyLearning.com's platform across four countries, each with complete data sovereignty and regulatory compliance.

The results were remarkable:

**Technical Achievements:**
- Deployed identical platform configurations across 4 AWS accounts
- Maintained 99.9% uptime during the migration process
- Achieved sub-200ms response times in all target regions
- Successfully handled 50,000+ concurrent users across all deployments

**Business Impact:**
- EduGlobal's user satisfaction scores increased by 40% within the first month
- Platform performance improved by 300% compared to their legacy system
- Time-to-market for new features reduced from months to weeks
- Combined user base of both platforms exceeded 3 million students

**Operational Benefits:**
- Automated deployment process reduced setup time from weeks to hours
- Standardized monitoring and alerting across all deployments
- Centralized security and compliance management
- Simplified update and maintenance procedures

*"The cross-account sharing capability transformed what could have been a complex, months-long integration into a streamlined, automated process,"* Dr. Chen reflected during their three-month partnership review. *"We maintained complete control over our data and compliance requirements while gaining access to a world-class platform."*

### The Broader Implications

The success of the EduGlobal partnership opened MyLearning.com's eyes to the broader possibilities of AWS cross-account collaboration. They realized they had built not just a platform, but a scalable technology solution that could be shared, licensed, and deployed across multiple organizations while maintaining security and compliance.

This realization led to the development of their "Platform-as-a-Service" offering, where educational institutions could license their technology stack and deploy it in their own AWS accounts. The cross-account sharing capabilities became the foundation for a new revenue stream that would eventually account for 30% of their total revenue.

The partnership also demonstrated the power of AWS's multi-account architecture for complex organizational relationships. By maintaining strict account boundaries while enabling selective resource sharing, they could collaborate with partners without compromising security or compliance requirements.

*"Cross-account sharing isn't just a technical feature,"* Amit observed as they planned their next partnership. *"It's an enabler for new business models. We can now scale our technology impact far beyond what we could achieve with a single-account, single-organization approach."*

---

*The success of cross-account AMI sharing had proven that AWS's multi-account architecture could support sophisticated business relationships while maintaining security and compliance. But the team was about to discover that their advanced EC2 capabilities could be further enhanced through integration with other AWS services, creating even more powerful and resilient architectures.*