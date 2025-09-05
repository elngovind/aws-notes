# Load Balancer Deep Dive

## Chapter 42: Mastering AWS Load Balancers (July 2023)

### The Load Balancer Selection Process
After the midnight crisis, Raj and his team spent the next week deep-diving into AWS load balancing options. Each type served different purposes, and choosing the wrong one could mean another outage.

*"We learned that not all load balancers are created equal. It's like choosing between a bicycle, a car, and a rocket ship—they all get you places, but the journey and destination matter."* - Amit (Lead Developer)

## Classic Load Balancer (CLB) - The Legacy Option

### Understanding CLB (Deprecated but Important)
```
Classic Load Balancer Overview:
├── Launch Date: 2009 (AWS's first load balancer)
├── Current Status: DEPRECATED (no new features)
├── Migration Deadline: No hard deadline, but strongly recommended
├── Operating Layers: Layer 4 (TCP/SSL) and Layer 7 (HTTP/HTTPS)
├── Key Limitations:
│   ├── No advanced routing capabilities
│   ├── Limited health check options
│   ├── No WebSocket support
│   ├── No HTTP/2 support
│   ├── No container integration
│   ├── Basic SSL/TLS termination
│   └── Limited monitoring capabilities
└── Why It's Deprecated:
    ├── Replaced by more advanced ALB and NLB
    ├── Limited scalability features
    ├── Lacks modern application requirements
    ├── No microservices support
    └── Higher operational overhead
```

### CLB Configuration Example (For Reference Only)
```python
# Classic Load Balancer Configuration (DEPRECATED - DO NOT USE)
import boto3

def create_classic_load_balancer():
    """
    DEPRECATED: This is for educational purposes only
    Use ALB or NLB for new implementations
    """
    elb_client = boto3.client('elb', region_name='ap-south-1')
    
    # This is how CLB was configured (deprecated)
    response = elb_client.create_load_balancer(
        LoadBalancerName='MyLearning-CLB-DEPRECATED',
        Listeners=[
            {
                'Protocol': 'HTTP',
                'LoadBalancerPort': 80,
                'InstancePort': 80,
                'InstanceProtocol': 'HTTP'
            },
            {
                'Protocol': 'HTTPS',
                'LoadBalancerPort': 443,
                'InstancePort': 80,
                'InstanceProtocol': 'HTTP',
                'SSLCertificateId': 'arn:aws:acm:ap-south-1:123456789012:certificate/12345678-1234-1234-1234-123456789012'
            }
        ],
        AvailabilityZones=['ap-south-1a', 'ap-south-1b'],
        Scheme='internet-facing'
    )
    
    print("WARNING: Classic Load Balancer is deprecated!")
    print("Migrate to Application Load Balancer (ALB) or Network Load Balancer (NLB)")
    return response

# Migration Path from CLB
def migrate_from_clb_to_alb():
    """Migration strategy from CLB to ALB"""
    migration_steps = {
        'assessment': [
            'Inventory existing CLB configurations',
            'Identify traffic patterns and requirements',
            'Document current health check configurations',
            'Review SSL certificate usage',
            'Analyze security group configurations'
        ],
        'planning': [
            'Design ALB target group structure',
            'Plan listener and routing rules',
            'Schedule migration window',
            'Prepare rollback procedures',
            'Update DNS configurations'
        ],
        'execution': [
            'Create ALB with equivalent configuration',
            'Set up target groups and health checks',
            'Configure listeners and SSL certificates',
            'Test ALB functionality thoroughly',
            'Update Route 53 records with weighted routing',
            'Monitor traffic distribution',
            'Gradually shift traffic to ALB',
            'Decommission CLB after validation'
        ]
    }
    return migration_steps
```

## Application Load Balancer (ALB) - Layer 7 Intelligence

### ALB Core Concepts and Components
```
Application Load Balancer Architecture:
├── Load Balancer
│   ├── Internet-facing or Internal
│   ├── Operates in multiple AZs (minimum 2)
│   ├── Elastic IP not supported (dynamic IPs)
│   └── Integrated with AWS services
├── Listeners
│   ├── Process that checks for connection requests
│   ├── Protocol: HTTP, HTTPS
│   ├── Port: 1-65535
│   ├── Rules: Determine how to route requests
│   └── Default actions: Forward, redirect, fixed response
├── Target Groups
│   ├── Route requests to registered targets
│   ├── Target types: Instance, IP, Lambda, ALB
│   ├── Health checks: HTTP, HTTPS
│   ├── Load balancing algorithms: Round robin, least outstanding requests
│   └── Sticky sessions support
├── Rules and Routing
│   ├── Host-based routing: route.mylearning.com
│   ├── Path-based routing: /api/*, /admin/*
│   ├── Header-based routing: User-Agent, Custom headers
│   ├── Query parameter routing: ?version=v2
│   ├── Source IP routing: CIDR blocks
│   └── HTTP method routing: GET, POST, PUT, DELETE
└── Advanced Features
    ├── SSL/TLS termination and passthrough
    ├── WebSocket support
    ├── HTTP/2 support
    ├── Server Name Indication (SNI)
    ├── WAF integration
    ├── Cognito authentication
    ├── OIDC authentication
    └── Lambda function integration
```

### ALB Implementation for MyLearning.com
```python
import boto3
import json

class MyLearningALBSetup:
    def __init__(self):
        self.elbv2_client = boto3.client('elbv2', region_name='ap-south-1')
        self.ec2_client = boto3.client('ec2', region_name='ap-south-1')
        
    def create_application_load_balancer(self):
        """Create ALB for MyLearning.com"""
        
        # Get VPC and subnet information
        vpc_id = 'vpc-12345678'  # MyLearning VPC
        public_subnets = ['subnet-12345678', 'subnet-87654321']  # Multi-AZ
        
        # Create security group for ALB
        alb_sg = self.create_alb_security_group(vpc_id)
        
        # Create the Application Load Balancer
        alb_response = self.elbv2_client.create_load_balancer(
            Name='MyLearning-ALB-Production',
            Subnets=public_subnets,
            SecurityGroups=[alb_sg['GroupId']],
            Scheme='internet-facing',
            Tags=[
                {'Key': 'Name', 'Value': 'MyLearning-ALB-Production'},
                {'Key': 'Environment', 'Value': 'Production'},
                {'Key': 'Project', 'Value': 'MyLearning-Platform'},
                {'Key': 'CostCenter', 'Value': 'Engineering'},
                {'Key': 'Owner', 'Value': 'DevOps-Team'}
            ],
            Type='application',
            IpAddressType='ipv4'
        )
        
        alb_arn = alb_response['LoadBalancers'][0]['LoadBalancerArn']
        alb_dns = alb_response['LoadBalancers'][0]['DNSName']
        
        print(f"ALB Created: {alb_arn}")
        print(f"ALB DNS: {alb_dns}")
        
        return alb_response
    
    def create_alb_security_group(self, vpc_id):
        """Create security group for ALB"""
        
        sg_response = self.ec2_client.create_security_group(
            GroupName='MyLearning-ALB-SecurityGroup',
            Description='Security group for MyLearning Application Load Balancer',
            VpcId=vpc_id
        )
        
        sg_id = sg_response['GroupId']
        
        # Add inbound rules
        self.ec2_client.authorize_security_group_ingress(
            GroupId=sg_id,
            IpPermissions=[
                {
                    'IpProtocol': 'tcp',
                    'FromPort': 80,
                    'ToPort': 80,
                    'IpRanges': [{'CidrIp': '0.0.0.0/0', 'Description': 'HTTP from Internet'}]
                },
                {
                    'IpProtocol': 'tcp',
                    'FromPort': 443,
                    'ToPort': 443,
                    'IpRanges': [{'CidrIp': '0.0.0.0/0', 'Description': 'HTTPS from Internet'}]
                }
            ]
        )
        
        return sg_response
    
    def create_target_groups(self, vpc_id):
        """Create target groups for different services"""
        
        target_groups = {}
        
        # Web Application Target Group
        web_tg = self.elbv2_client.create_target_group(
            Name='MyLearning-Web-TG',
            Protocol='HTTP',
            Port=80,
            VpcId=vpc_id,
            HealthCheckProtocol='HTTP',
            HealthCheckPath='/health',
            HealthCheckIntervalSeconds=30,
            HealthCheckTimeoutSeconds=5,
            HealthyThresholdCount=2,
            UnhealthyThresholdCount=3,
            Matcher={'HttpCode': '200'},
            TargetType='instance',
            Tags=[
                {'Key': 'Name', 'Value': 'MyLearning-Web-TargetGroup'},
                {'Key': 'Service', 'Value': 'WebApplication'}
            ]
        )
        target_groups['web'] = web_tg['TargetGroups'][0]['TargetGroupArn']
        
        # API Target Group
        api_tg = self.elbv2_client.create_target_group(
            Name='MyLearning-API-TG',
            Protocol='HTTP',
            Port=8000,
            VpcId=vpc_id,
            HealthCheckProtocol='HTTP',
            HealthCheckPath='/api/health',
            HealthCheckIntervalSeconds=15,
            HealthCheckTimeoutSeconds=5,
            HealthyThresholdCount=2,
            UnhealthyThresholdCount=2,
            Matcher={'HttpCode': '200'},
            TargetType='instance'
        )
        target_groups['api'] = api_tg['TargetGroups'][0]['TargetGroupArn']
        
        # Admin Panel Target Group
        admin_tg = self.elbv2_client.create_target_group(
            Name='MyLearning-Admin-TG',
            Protocol='HTTP',
            Port=9000,
            VpcId=vpc_id,
            HealthCheckProtocol='HTTP',
            HealthCheckPath='/admin/health',
            HealthCheckIntervalSeconds=30,
            HealthCheckTimeoutSeconds=10,
            HealthyThresholdCount=3,
            UnhealthyThresholdCount=3,
            Matcher={'HttpCode': '200,302'},
            TargetType='instance'
        )
        target_groups['admin'] = admin_tg['TargetGroups'][0]['TargetGroupArn']
        
        return target_groups
    
    def create_listeners_and_rules(self, alb_arn, target_groups, certificate_arn):
        """Create listeners and routing rules"""
        
        # HTTP Listener (redirect to HTTPS)
        http_listener = self.elbv2_client.create_listener(
            LoadBalancerArn=alb_arn,
            Protocol='HTTP',
            Port=80,
            DefaultActions=[
                {
                    'Type': 'redirect',
                    'RedirectConfig': {
                        'Protocol': 'HTTPS',
                        'Port': '443',
                        'StatusCode': 'HTTP_301'
                    }
                }
            ]
        )
        
        # HTTPS Listener with routing rules
        https_listener = self.elbv2_client.create_listener(
            LoadBalancerArn=alb_arn,
            Protocol='HTTPS',
            Port=443,
            Certificates=[{'CertificateArn': certificate_arn}],
            DefaultActions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': target_groups['web']
                }
            ]
        )
        
        https_listener_arn = https_listener['Listeners'][0]['ListenerArn']
        
        # Create routing rules
        rules = []
        
        # API routing rule
        api_rule = self.elbv2_client.create_rule(
            ListenerArn=https_listener_arn,
            Priority=100,
            Conditions=[
                {
                    'Field': 'path-pattern',
                    'Values': ['/api/*']
                }
            ],
            Actions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': target_groups['api']
                }
            ]
        )
        rules.append(api_rule)
        
        # Admin routing rule
        admin_rule = self.elbv2_client.create_rule(
            ListenerArn=https_listener_arn,
            Priority=200,
            Conditions=[
                {
                    'Field': 'path-pattern',
                    'Values': ['/admin/*']
                }
            ],
            Actions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': target_groups['admin']
                }
            ]
        )
        rules.append(admin_rule)
        
        # Mobile API routing (based on User-Agent)
        mobile_rule = self.elbv2_client.create_rule(
            ListenerArn=https_listener_arn,
            Priority=150,
            Conditions=[
                {
                    'Field': 'http-header',
                    'HttpHeaderConfig': {
                        'HttpHeaderName': 'User-Agent',
                        'Values': ['*Mobile*', '*Android*', '*iPhone*']
                    }
                },
                {
                    'Field': 'path-pattern',
                    'Values': ['/api/*']
                }
            ],
            Actions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': target_groups['api']
                }
            ]
        )
        rules.append(mobile_rule)
        
        return {
            'http_listener': http_listener,
            'https_listener': https_listener,
            'rules': rules
        }

# Implementation
alb_setup = MyLearningALBSetup()

# Step 1: Create ALB
alb = alb_setup.create_application_load_balancer()

# Step 2: Create Target Groups
target_groups = alb_setup.create_target_groups('vpc-12345678')

# Step 3: Create Listeners and Rules
certificate_arn = 'arn:aws:acm:ap-south-1:123456789012:certificate/12345678-1234-1234-1234-123456789012'
listeners = alb_setup.create_listeners_and_rules(
    alb['LoadBalancers'][0]['LoadBalancerArn'],
    target_groups,
    certificate_arn
)

print("MyLearning.com ALB Setup Complete!")
print(f"Target Groups: {len(target_groups)}")
print(f"Routing Rules: {len(listeners['rules'])}")
```

## Network Load Balancer (NLB) - Layer 4 Performance

### NLB Core Concepts
```
Network Load Balancer Characteristics:
├── Performance
│   ├── Millions of requests per second
│   ├── Ultra-low latency (microseconds)
│   ├── Sudden traffic spikes handling
│   └── High throughput capabilities
├── Protocol Support
│   ├── TCP (Transmission Control Protocol)
│   ├── UDP (User Datagram Protocol)
│   ├── TLS (Transport Layer Security)
│   └── TCP_UDP (Dual protocol support)
├── IP Address Features
│   ├── Static IP addresses per AZ
│   ├── Elastic IP address support
│   ├── Source IP preservation
│   └── Client IP visibility to targets
├── Target Types
│   ├── EC2 instances
│   ├── IP addresses (including on-premises)
│   ├── Application Load Balancers
│   └── Lambda functions (limited)
└── Use Cases
    ├── Gaming applications
    ├── IoT platforms
    ├── Real-time communications
    ├── Financial trading systems
    ├── Video streaming
    └── High-frequency applications
```

### NLB Implementation Example
```python
class MyLearningNLBSetup:
    def __init__(self):
        self.elbv2_client = boto3.client('elbv2', region_name='ap-south-1')
        self.ec2_client = boto3.client('ec2', region_name='ap-south-1')
    
    def create_network_load_balancer(self):
        """Create NLB for high-performance requirements"""
        
        # Allocate Elastic IPs for static IP addresses
        eip_1 = self.ec2_client.allocate_address(Domain='vpc')
        eip_2 = self.ec2_client.allocate_address(Domain='vpc')
        
        # Create NLB with static IPs
        nlb_response = self.elbv2_client.create_load_balancer(
            Name='MyLearning-NLB-Gaming',
            Subnets=[
                {
                    'SubnetId': 'subnet-12345678',
                    'AllocationId': eip_1['AllocationId']
                },
                {
                    'SubnetId': 'subnet-87654321',
                    'AllocationId': eip_2['AllocationId']
                }
            ],
            Scheme='internet-facing',
            Type='network',
            IpAddressType='ipv4',
            Tags=[
                {'Key': 'Name', 'Value': 'MyLearning-NLB-Gaming'},
                {'Key': 'Environment', 'Value': 'Production'},
                {'Key': 'UseCase', 'Value': 'Gaming-Platform'}
            ]
        )
        
        return nlb_response
    
    def create_nlb_target_group(self, vpc_id):
        """Create target group for NLB"""
        
        # TCP target group for gaming servers
        tcp_tg = self.elbv2_client.create_target_group(
            Name='MyLearning-Gaming-TCP-TG',
            Protocol='TCP',
            Port=7777,  # Gaming server port
            VpcId=vpc_id,
            HealthCheckProtocol='TCP',
            HealthCheckPort='7777',
            HealthCheckIntervalSeconds=10,
            HealthyThresholdCount=2,
            UnhealthyThresholdCount=2,
            TargetType='instance'
        )
        
        # UDP target group for real-time communications
        udp_tg = self.elbv2_client.create_target_group(
            Name='MyLearning-Gaming-UDP-TG',
            Protocol='UDP',
            Port=7778,
            VpcId=vpc_id,
            HealthCheckProtocol='TCP',
            HealthCheckPort='7777',  # Use TCP for health checks
            HealthCheckIntervalSeconds=10,
            HealthyThresholdCount=2,
            UnhealthyThresholdCount=2,
            TargetType='instance'
        )
        
        return {
            'tcp': tcp_tg['TargetGroups'][0]['TargetGroupArn'],
            'udp': udp_tg['TargetGroups'][0]['TargetGroupArn']
        }
    
    def create_nlb_listeners(self, nlb_arn, target_groups):
        """Create NLB listeners"""
        
        # TCP Listener
        tcp_listener = self.elbv2_client.create_listener(
            LoadBalancerArn=nlb_arn,
            Protocol='TCP',
            Port=7777,
            DefaultActions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': target_groups['tcp']
                }
            ]
        )
        
        # UDP Listener
        udp_listener = self.elbv2_client.create_listener(
            LoadBalancerArn=nlb_arn,
            Protocol='UDP',
            Port=7778,
            DefaultActions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': target_groups['udp']
                }
            ]
        )
        
        return {
            'tcp_listener': tcp_listener,
            'udp_listener': udp_listener
        }

# When to use NLB vs ALB
def load_balancer_decision_matrix():
    """Decision matrix for choosing between ALB and NLB"""
    
    decision_factors = {
        'ALB_preferred': {
            'protocols': ['HTTP', 'HTTPS', 'WebSocket'],
            'routing_needs': ['Path-based', 'Host-based', 'Header-based'],
            'features_needed': ['SSL termination', 'WAF integration', 'Authentication'],
            'applications': ['Web applications', 'APIs', 'Microservices'],
            'latency_tolerance': 'Moderate (milliseconds)',
            'throughput_needs': 'High (thousands of requests/second)'
        },
        'NLB_preferred': {
            'protocols': ['TCP', 'UDP', 'TLS'],
            'routing_needs': ['Simple port-based routing'],
            'features_needed': ['Static IPs', 'Source IP preservation', 'Ultra-low latency'],
            'applications': ['Gaming', 'IoT', 'Real-time systems', 'Financial trading'],
            'latency_tolerance': 'Ultra-low (microseconds)',
            'throughput_needs': 'Extreme (millions of requests/second)'
        }
    }
    
    return decision_factors
```

## Gateway Load Balancer (GLB) - Layer 3 Gateway

### GLB Overview and Use Cases
```
Gateway Load Balancer Concepts:
├── Purpose
│   ├── Deploy and scale third-party network appliances
│   ├── Transparent network gateway functionality
│   ├── Inline traffic inspection and processing
│   └── Centralized security appliance management
├── Supported Appliances
│   ├── Firewalls (Palo Alto, Fortinet, Check Point)
│   ├── Intrusion Detection/Prevention Systems (IDS/IPS)
│   ├── Deep Packet Inspection (DPI) appliances
│   ├── Network monitoring tools
│   ├── Data loss prevention (DLP) systems
│   └── Custom security appliances
├── Technical Features
│   ├── GENEVE protocol encapsulation
│   ├── Layer 3 gateway functionality
│   ├── Transparent proxy capabilities
│   ├── Flow hash-based routing
│   ├── Health checking for appliances
│   └── Auto scaling integration
└── Architecture Benefits
    ├── Centralized security policy enforcement
    ├── Scalable appliance deployment
    ├── Simplified network architecture
    ├── Vendor-agnostic approach
    └── Cloud-native security integration
```

### GLB Implementation Example
```python
class MyLearningGLBSetup:
    def __init__(self):
        self.elbv2_client = boto3.client('elbv2', region_name='ap-south-1')
    
    def create_gateway_load_balancer(self):
        """Create GLB for security appliances"""
        
        # Note: GLB is typically used for security appliances
        # This example shows integration with firewall appliances
        
        glb_response = self.elbv2_client.create_load_balancer(
            Name='MyLearning-GLB-Security',
            Subnets=['subnet-12345678', 'subnet-87654321'],
            Type='gateway',
            Scheme='internal',  # GLB is typically internal
            Tags=[
                {'Key': 'Name', 'Value': 'MyLearning-GLB-Security'},
                {'Key': 'Purpose', 'Value': 'Security-Appliances'},
                {'Key': 'Environment', 'Value': 'Production'}
            ]
        )
        
        return glb_response
    
    def create_glb_target_group(self, vpc_id):
        """Create target group for security appliances"""
        
        # Target group for firewall appliances
        firewall_tg = self.elbv2_client.create_target_group(
            Name='MyLearning-Firewall-TG',
            Protocol='GENEVE',
            Port=6081,  # GENEVE default port
            VpcId=vpc_id,
            HealthCheckProtocol='TCP',
            HealthCheckPort='22',  # SSH port for appliance health check
            HealthCheckIntervalSeconds=10,
            HealthyThresholdCount=2,
            UnhealthyThresholdCount=2,
            TargetType='instance'
        )
        
        return firewall_tg['TargetGroups'][0]['TargetGroupArn']

# GLB is specialized - brief overview sufficient for most use cases
print("Gateway Load Balancer is specialized for security appliances")
print("Most web applications will use ALB or NLB")
```

### Load Balancer Comparison Summary

```python
def load_balancer_comparison():
    """Comprehensive comparison of AWS Load Balancers"""
    
    comparison = {
        'Classic_LB': {
            'status': 'DEPRECATED',
            'layer': 'Layer 4 & 7',
            'protocols': ['HTTP', 'HTTPS', 'TCP', 'SSL'],
            'use_case': 'Legacy applications (migrate to ALB/NLB)',
            'key_limitation': 'No advanced features'
        },
        'Application_LB': {
            'status': 'RECOMMENDED',
            'layer': 'Layer 7',
            'protocols': ['HTTP', 'HTTPS', 'WebSocket'],
            'use_case': 'Web applications, APIs, microservices',
            'key_features': ['Content-based routing', 'SSL termination', 'WAF integration']
        },
        'Network_LB': {
            'status': 'RECOMMENDED',
            'layer': 'Layer 4',
            'protocols': ['TCP', 'UDP', 'TLS'],
            'use_case': 'High-performance, gaming, IoT',
            'key_features': ['Ultra-low latency', 'Static IPs', 'Source IP preservation']
        },
        'Gateway_LB': {
            'status': 'SPECIALIZED',
            'layer': 'Layer 3',
            'protocols': ['GENEVE'],
            'use_case': 'Security appliances, network functions',
            'key_features': ['Transparent proxy', 'Appliance scaling', 'GENEVE protocol']
        }
    }
    
    return comparison

# MyLearning.com Final Decision
mylearning_decision = {
    'primary_choice': 'Application Load Balancer (ALB)',
    'reasoning': [
        'Web application with HTTP/HTTPS traffic',
        'Need for path-based routing (/api/, /admin/)',
        'SSL termination requirements',
        'Future microservices architecture',
        'Integration with AWS WAF for security'
    ],
    'secondary_choice': 'Network Load Balancer (NLB)',
    'nlb_use_case': 'Future gaming platform with real-time features',
    'implementation_timeline': 'ALB first, NLB for gaming features later'
}

print("MyLearning.com Load Balancer Strategy:")
print(f"Primary: {mylearning_decision['primary_choice']}")
print("Reasons:")
for reason in mylearning_decision['reasoning']:
    print(f"  • {reason}")
```

---

*"Choosing the right load balancer is like choosing the right tool for the job. You can hammer a nail with a wrench, but why would you when you have a hammer?"* - Raj (CTO)

### Key Takeaways

1. **CLB is Deprecated**: Migrate existing CLBs to ALB or NLB
2. **ALB for Web Applications**: Layer 7 features for HTTP/HTTPS traffic
3. **NLB for Performance**: Layer 4 for ultra-low latency and high throughput
4. **GLB for Security**: Specialized for network security appliances
5. **Choose Based on Requirements**: Protocol, performance, and feature needs determine the right choice