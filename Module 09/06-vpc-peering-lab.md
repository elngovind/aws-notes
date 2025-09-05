# Hands-on Lab: VPC Peering (Same Account, Same Region)

## Lab Overview
This hands-on lab will guide you through creating VPC peering connections between two VPCs in the same AWS account and region, following MyLearning.com's implementation approach.

## Prerequisites
- AWS account with appropriate permissions
- Basic understanding of VPC concepts
- Access to AWS Management Console

## Lab Architecture
```
Target Architecture:
┌─────────────────────────────────────────┐
│  VPC-A (MyLearning-Prod)               │
│  CIDR: 10.0.0.0/16                     │
│  ├── Public Subnet: 10.0.1.0/24       │
│  ├── Private Subnet: 10.0.2.0/24      │
│  └── EC2 Instance: Web Server          │
└─────────────┬───────────────────────────┘
              │ VPC Peering Connection
              │ (pcx-prod-analytics)
┌─────────────▼───────────────────────────┐
│  VPC-B (MyLearning-Analytics)          │
│  CIDR: 10.1.0.0/16                     │
│  ├── Public Subnet: 10.1.1.0/24       │
│  ├── Private Subnet: 10.1.2.0/24      │
│  └── EC2 Instance: Analytics Server    │
└─────────────────────────────────────────┘
```

## Step-by-Step Implementation

### Step 1: Create First VPC (MyLearning-Prod)

#### 1.1 Navigate to VPC Console
1. Open AWS Management Console
2. Search for "VPC" in the services search bar
3. Click on "VPC" to open the VPC Dashboard

#### 1.2 Create VPC-A
1. Click **"Create VPC"**
2. Select **"VPC and more"** (recommended for beginners)
3. Configure the following settings:
   ```
   Name tag auto-generation: MyLearning-Prod
   IPv4 CIDR block: 10.0.0.0/16
   IPv6 CIDR block: No IPv6 CIDR block
   Tenancy: Default
   
   Number of Availability Zones: 2
   Number of public subnets: 2
   Number of private subnets: 2
   
   NAT gateways: In 1 AZ
   VPC endpoints: None
   
   DNS options:
   ✓ Enable DNS hostnames
   ✓ Enable DNS resolution
   ```
4. Click **"Create VPC"**
5. Wait for the VPC creation to complete (approximately 2-3 minutes)

#### 1.3 Verify VPC-A Creation
1. In the VPC Dashboard, click **"Your VPCs"**
2. Verify that "MyLearning-Prod-vpc" is created with CIDR 10.0.0.0/16
3. Note down the VPC ID (e.g., vpc-0123456789abcdef0)

### Step 2: Create Second VPC (MyLearning-Analytics)

#### 2.1 Create VPC-B
1. Click **"Create VPC"** again
2. Select **"VPC and more"**
3. Configure the following settings:
   ```
   Name tag auto-generation: MyLearning-Analytics
   IPv4 CIDR block: 10.1.0.0/16
   IPv6 CIDR block: No IPv6 CIDR block
   Tenancy: Default
   
   Number of Availability Zones: 2
   Number of public subnets: 2
   Number of private subnets: 2
   
   NAT gateways: In 1 AZ
   VPC endpoints: None
   
   DNS options:
   ✓ Enable DNS hostnames
   ✓ Enable DNS resolution
   ```
4. Click **"Create VPC"**
5. Wait for the VPC creation to complete

#### 2.2 Verify VPC-B Creation
1. Verify that "MyLearning-Analytics-vpc" is created with CIDR 10.1.0.0/16
2. Note down the VPC ID (e.g., vpc-0987654321fedcba0)

### Step 3: Create VPC Peering Connection

#### 3.1 Initiate Peering Connection
1. In the VPC Dashboard, click **"Peering connections"** in the left navigation
2. Click **"Create peering connection"**
3. Configure the peering connection:
   ```
   Peering connection name tag: MyLearning-Prod-Analytics-Peering
   
   VPC (Requester):
   - Select "MyLearning-Prod-vpc" from dropdown
   
   Account: My account
   Region: This region (ap-south-1)
   
   VPC (Accepter):
   - Select "MyLearning-Analytics-vpc" from dropdown
   ```
4. Click **"Create peering connection"**

#### 3.2 Accept Peering Connection
1. The peering connection will be in "Pending Acceptance" state
2. Select the newly created peering connection
3. Click **"Actions"** → **"Accept request"**
4. In the confirmation dialog, click **"Accept request"**
5. The status should change to "Active"

#### 3.3 Note Peering Connection ID
1. Note down the Peering Connection ID (e.g., pcx-0123456789abcdef0)
2. This will be used in route table configuration

### Step 4: Configure Route Tables

#### 4.1 Update Route Table for VPC-A (MyLearning-Prod)
1. Click **"Route tables"** in the left navigation
2. Find the route table associated with MyLearning-Prod VPC
   - Look for route table with VPC ID matching MyLearning-Prod-vpc
   - It should be named "MyLearning-Prod-rtb-private" or similar
3. Select the **private route table** for MyLearning-Prod
4. Click **"Routes"** tab
5. Click **"Edit routes"**
6. Click **"Add route"**
7. Configure the new route:
   ```
   Destination: 10.1.0.0/16
   Target: Peering Connection → Select your peering connection
   ```
8. Click **"Save changes"**

#### 4.2 Update Route Table for VPC-B (MyLearning-Analytics)
1. Find the route table associated with MyLearning-Analytics VPC
2. Select the **private route table** for MyLearning-Analytics
3. Click **"Routes"** tab
4. Click **"Edit routes"**
5. Click **"Add route"**
6. Configure the new route:
   ```
   Destination: 10.0.0.0/16
   Target: Peering Connection → Select your peering connection
   ```
7. Click **"Save changes"**

#### 4.3 Update Public Route Tables (Optional)
Repeat the same process for public route tables if you want public subnets to communicate across VPCs.

### Step 5: Create Security Groups

#### 5.1 Create Security Group for VPC-A
1. Click **"Security groups"** in the left navigation
2. Click **"Create security group"**
3. Configure security group:
   ```
   Security group name: MyLearning-Prod-SG
   Description: Security group for MyLearning Production VPC
   VPC: Select MyLearning-Prod-vpc
   
   Inbound rules:
   - Type: SSH, Protocol: TCP, Port: 22, Source: My IP
   - Type: HTTP, Protocol: TCP, Port: 80, Source: 0.0.0.0/0
   - Type: ICMP, Protocol: ICMP, Port: All, Source: 10.1.0.0/16
   - Type: SSH, Protocol: TCP, Port: 22, Source: 10.1.0.0/16
   
   Outbound rules:
   - Type: All traffic, Protocol: All, Port: All, Destination: 0.0.0.0/0
   ```
4. Click **"Create security group"**

#### 5.2 Create Security Group for VPC-B
1. Click **"Create security group"**
2. Configure security group:
   ```
   Security group name: MyLearning-Analytics-SG
   Description: Security group for MyLearning Analytics VPC
   VPC: Select MyLearning-Analytics-vpc
   
   Inbound rules:
   - Type: SSH, Protocol: TCP, Port: 22, Source: My IP
   - Type: HTTP, Protocol: TCP, Port: 80, Source: 0.0.0.0/0
   - Type: ICMP, Protocol: ICMP, Port: All, Source: 10.0.0.0/16
   - Type: SSH, Protocol: TCP, Port: 22, Source: 10.0.0.0/16
   
   Outbound rules:
   - Type: All traffic, Protocol: All, Port: All, Destination: 0.0.0.0/0
   ```
3. Click **"Create security group"**

### Step 6: Launch EC2 Instances

#### 6.1 Launch Instance in VPC-A
1. Navigate to **EC2 Console**
2. Click **"Launch instance"**
3. Configure instance:
   ```
   Name: MyLearning-Prod-Server
   
   Application and OS Images:
   - Amazon Linux 2023 AMI (Free tier eligible)
   
   Instance type:
   - t2.micro (Free tier eligible)
   
   Key pair:
   - Create new key pair or select existing
   - Name: MyLearning-KeyPair
   - Type: RSA, Format: .pem
   
   Network settings:
   - VPC: MyLearning-Prod-vpc
   - Subnet: MyLearning-Prod-subnet-private1 (or any private subnet)
   - Auto-assign public IP: Disable
   - Security group: MyLearning-Prod-SG
   
   User data (Advanced details):
   ```
   ```bash
   #!/bin/bash
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd
   echo "<h1>MyLearning Production Server</h1>" > /var/www/html/index.html
   echo "<p>VPC: MyLearning-Prod (10.0.0.0/16)</p>" >> /var/www/html/index.html
   echo "<p>Instance IP: $(curl -s http://169.254.169.254/latest/meta-data/local-ipv4)</p>" >> /var/www/html/index.html
   ```
4. Click **"Launch instance"**

#### 6.2 Launch Instance in VPC-B
1. Click **"Launch instance"** again
2. Configure instance:
   ```
   Name: MyLearning-Analytics-Server
   
   Application and OS Images:
   - Amazon Linux 2023 AMI (Free tier eligible)
   
   Instance type:
   - t2.micro (Free tier eligible)
   
   Key pair:
   - Use same key pair as previous instance
   
   Network settings:
   - VPC: MyLearning-Analytics-vpc
   - Subnet: MyLearning-Analytics-subnet-private1
   - Auto-assign public IP: Disable
   - Security group: MyLearning-Analytics-SG
   
   User data:
   ```
   ```bash
   #!/bin/bash
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd
   echo "<h1>MyLearning Analytics Server</h1>" > /var/www/html/index.html
   echo "<p>VPC: MyLearning-Analytics (10.1.0.0/16)</p>" >> /var/www/html/index.html
   echo "<p>Instance IP: $(curl -s http://169.254.169.254/latest/meta-data/local-ipv4)</p>" >> /var/www/html/index.html
   ```
3. Click **"Launch instance"**

### Step 7: Create Bastion Host (For Testing)

#### 7.1 Launch Bastion Host in VPC-A
1. Click **"Launch instance"**
2. Configure bastion host:
   ```
   Name: MyLearning-Bastion
   
   Application and OS Images:
   - Amazon Linux 2023 AMI
   
   Instance type:
   - t2.micro
   
   Key pair:
   - Use same key pair
   
   Network settings:
   - VPC: MyLearning-Prod-vpc
   - Subnet: MyLearning-Prod-subnet-public1 (public subnet)
   - Auto-assign public IP: Enable
   - Security group: Create new security group
     - Name: MyLearning-Bastion-SG
     - SSH access from My IP only
   ```
3. Click **"Launch instance"**

### Step 8: Test VPC Peering Connection

#### 8.1 Connect to Bastion Host
1. Wait for all instances to be in "Running" state
2. Note the public IP of the bastion host
3. Connect via SSH:
   ```bash
   ssh -i MyLearning-KeyPair.pem ec2-user@<bastion-public-ip>
   ```

#### 8.2 Test Connectivity to VPC-A Instance
1. From bastion host, get the private IP of the production server
2. Test ping connectivity:
   ```bash
   # Get private IP from EC2 console (e.g., 10.0.2.100)
   ping 10.0.2.100
   ```
3. Test SSH connectivity:
   ```bash
   ssh 10.0.2.100
   ```
4. Test HTTP connectivity:
   ```bash
   curl http://10.0.2.100
   ```

#### 8.3 Test Cross-VPC Connectivity
1. From bastion host, test connectivity to analytics server:
   ```bash
   # Get private IP of analytics server (e.g., 10.1.2.200)
   ping 10.1.2.200
   ```
2. Test SSH connectivity across VPCs:
   ```bash
   ssh 10.1.2.200
   ```
3. Test HTTP connectivity across VPCs:
   ```bash
   curl http://10.1.2.200
   ```

#### 8.4 Test Bidirectional Connectivity
1. SSH to the production server:
   ```bash
   ssh 10.0.2.100
   ```
2. From production server, test connectivity to analytics server:
   ```bash
   ping 10.1.2.200
   curl http://10.1.2.200
   ```
3. SSH to analytics server from production server:
   ```bash
   ssh 10.1.2.200
   ```
4. From analytics server, test connectivity back to production:
   ```bash
   ping 10.0.2.100
   curl http://10.0.2.100
   ```

### Step 9: Verify DNS Resolution

#### 9.1 Test DNS Resolution
1. From any instance, test DNS resolution:
   ```bash
   # This should resolve to private IP
   nslookup ip-10-0-2-100.ap-south-1.compute.internal
   nslookup ip-10-1-2-200.ap-south-1.compute.internal
   ```

#### 9.2 Enable DNS Resolution (if not working)
1. Go to VPC Console → Peering connections
2. Select your peering connection
3. Click **"Actions"** → **"Edit DNS settings"**
4. Enable both options:
   - ✓ DNS resolution for requester VPC
   - ✓ DNS resolution for accepter VPC
5. Click **"Save changes"**

### Step 10: Monitor VPC Peering

#### 10.1 Enable VPC Flow Logs
1. Go to VPC Console → Your VPCs
2. Select MyLearning-Prod-vpc
3. Click **"Flow logs"** tab
4. Click **"Create flow log"**
5. Configure flow log:
   ```
   Filter: All
   Maximum aggregation interval: 1 minute
   Destination: Send to CloudWatch Logs
   Destination log group: /aws/vpc/flowlogs
   IAM role: Create new role
   ```
6. Click **"Create flow log"**
7. Repeat for MyLearning-Analytics-vpc

#### 10.2 View CloudWatch Metrics
1. Go to CloudWatch Console
2. Click **"Metrics"** → **"All metrics"**
3. Look for VPC metrics
4. Monitor data transfer metrics

### Step 11: Troubleshooting Common Issues

#### 11.1 Connection Timeout Issues
```bash
# Check security group rules
aws ec2 describe-security-groups --group-ids sg-xxxxxxxxx

# Check route tables
aws ec2 describe-route-tables --route-table-ids rtb-xxxxxxxxx

# Check network ACLs
aws ec2 describe-network-acls --network-acl-ids acl-xxxxxxxxx
```

#### 11.2 DNS Resolution Issues
1. Verify DNS settings on peering connection
2. Check VPC DNS settings:
   - DNS resolution: Enabled
   - DNS hostnames: Enabled

#### 11.3 Routing Issues
1. Verify route tables have correct routes
2. Check route propagation
3. Verify CIDR blocks don't overlap

### Step 12: Clean Up Resources

#### 12.1 Terminate EC2 Instances
1. Go to EC2 Console → Instances
2. Select all instances created in this lab
3. Click **"Instance state"** → **"Terminate instance"**
4. Confirm termination

#### 12.2 Delete VPC Peering Connection
1. Go to VPC Console → Peering connections
2. Select the peering connection
3. Click **"Actions"** → **"Delete peering connection"**
4. Type "delete" to confirm

#### 12.3 Delete VPCs
1. Go to VPC Console → Your VPCs
2. Select MyLearning-Prod-vpc
3. Click **"Actions"** → **"Delete VPC"**
4. Type "delete" to confirm
5. Repeat for MyLearning-Analytics-vpc

#### 12.4 Delete Security Groups
Security groups will be automatically deleted when VPCs are deleted.

#### 12.5 Delete Key Pair
1. Go to EC2 Console → Key Pairs
2. Select MyLearning-KeyPair
3. Click **"Actions"** → **"Delete"**

## Lab Verification Checklist

- [ ] Two VPCs created with non-overlapping CIDR blocks
- [ ] VPC peering connection established and active
- [ ] Route tables updated with peering routes
- [ ] Security groups configured for cross-VPC communication
- [ ] EC2 instances launched in both VPCs
- [ ] Bastion host created for testing
- [ ] Ping connectivity working across VPCs
- [ ] SSH connectivity working across VPCs
- [ ] HTTP connectivity working across VPCs
- [ ] DNS resolution working (optional)
- [ ] VPC Flow Logs enabled for monitoring
- [ ] All resources cleaned up after testing

## Expected Results

After completing this lab, you should have:

1. **Successful VPC Peering**: Two VPCs connected via peering connection
2. **Bidirectional Connectivity**: Instances in both VPCs can communicate
3. **Proper Routing**: Traffic routes correctly through peering connection
4. **Security**: Only allowed traffic passes through security groups
5. **Monitoring**: Flow logs capture all traffic for analysis

## Common Troubleshooting Commands

```bash
# Test connectivity
ping <target-ip>
telnet <target-ip> <port>
curl http://<target-ip>

# Check routing
ip route show
traceroute <target-ip>

# Check DNS
nslookup <hostname>
dig <hostname>

# Check network configuration
ifconfig
netstat -rn
```

## Key Learnings

1. **CIDR Planning**: Non-overlapping CIDR blocks are essential
2. **Route Tables**: Must be updated on both sides of peering
3. **Security Groups**: Must allow traffic from peer VPC CIDR
4. **DNS Resolution**: Requires explicit enablement on peering connection
5. **Monitoring**: VPC Flow Logs provide valuable troubleshooting data

---

*This hands-on lab provides practical experience with VPC peering implementation, following the same patterns used by MyLearning.com in their production environment.*