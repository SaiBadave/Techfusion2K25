# Techfusion2K25
VPC hands-on

AWS VPC, NAT, and EC2 Demo – Step by Step

🎯 Demo Objective

Demonstrate a VPC with one public subnet and one private subnet, showing:

Public vs Private subnet behavior

Internet access via Internet Gateway (IGW) and NAT Gateway

EC2 instance connectivity


All steps are usable for a live demo or presentation.

Part 1: VPC-Only Demo (No EC2 Required)

Step 1️⃣: Create a Custom VPC

1. Go to AWS Console → VPC → Your VPCs → Create VPC
2. Configure:
Name tag: DemoVPC
IPv4 CIDR block: 10.0.0.0/16
Tenancy: Default

3. Click Create VPC

4. Result: A custom VPC is created.

Step 2️⃣: Create a Public Subnet

1. Go to VPC → Subnets → Create Subnet
2. Configure:
  Name tag: PublicSubnet
  VPC: DemoVPC
  Availability Zone: e.g., ap-south-1a
  IPv4 CIDR block: 10.0.1.0/24
3. Click Create subnet
4. Result: Public subnet is ready.


Step 3️⃣: Create a Private Subnet

1. Go to VPC → Subnets → Create Subnet
2. Configure:
  Name tag: PrivateSubnet
  VPC: DemoVPC
Availability Zone: e.g., ap-south-1b
IPv4 CIDR block: 10.0.2.0/24
3. Click Create subnet
4. Result: Private subnet is ready.
   

Step 4️⃣: Create and Attach an Internet Gateway (IGW)

1. Go to VPC → Internet Gateways → Create Internet Gateway
2. Name tag: DemoIGW
3. Click Create Internet Gateway
4. Select the IGW → Actions → Attach to VPC → DemoVPC



Step 5️⃣: Configure Route Tables

Public Route Table

1. Go to VPC → Route Tables → Create Route Table
2. Name tag: PublicRouteTable
3. VPC: DemoVPC
4. Click Create route table
5. Edit Routes → Add:
  Destination: 0.0.0.0/0
  Target: DemoIGW
6. Subnet Associations → Associate PublicSubnet



Private Route Table

1. Go to VPC → Route Tables → Create Route Table
2. Name tag: PrivateRouteTable
3. VPC: DemoVPC
4. Do NOT add route to IGW → ensures private subnet isolation
5. Subnet Associations → Associate PrivateSubnet
   

Step 6️⃣: Demonstrate Public vs Private Access

1. Subnet Details → Auto-assign Public IP:
  PublicSubnet → Enabled → Internet-accessible (if EC2 launched)
  PrivateSubnet → Disabled → Isolated
2. Route Tables Check:
  PublicSubnet → 0.0.0.0/0 → IGW
  PrivateSubnet → No IGW route
3. Optional: Use VPC Reachability Analyzer to show connectivity differences
4. Optional: Inspect default NACLs to show private subnet blocks inbound Internet

Step 7️⃣: Key Takeaways

Public subnet → Can access Internet
Private subnet → Cannot access Internet (isolated)
Route Tables + IGW control Internet connectivity
Subnets provide logical separation of workloads


Part 2: Attach NAT Gateway to Private Subnet
Step 1️⃣ – Go to NAT Gateway Creation

1. AWS Console → Services → VPC → NAT Gateways
2. Click Create NAT Gateway


Step 2️⃣ – Configure NAT Gateway

1. Name Tag: DemoNATGateway
2. Subnet: Select PublicSubnet (10.0.1.0/24)
NAT must reside in a public subnet to access IGW
3. Elastic IP Allocation: Allocate new Elastic IP


Step 3️⃣ – Create NAT Gateway

1. Click Create NAT Gateway
2. Wait for status → Available
3. Note NAT Gateway ID (e.g., nat-0a1b2c3d4e5f6g7h)


Step 4️⃣ – Update Private Subnet Route Table
1. Go to VPC → Route Tables → PrivateRouteTable
2. Edit Routes → Add:
  Destination: 0.0.0.0/0
  Target: NAT Gateway (nat-xxxxxxxx)
3. Save routes

✅ Private subnet instances can now access Internet outbound via NAT Gateway, but remain isolated inbound.


Step 5️⃣ – Verification

Checkpoint	Expected Configuration
  NAT Gateway	Public Subnet (10.0.1.0/24), has Elastic IP
  Private Route Table	0.0.0.0/0 → NAT Gateway
  Public Route Table	0.0.0.0/0 → IGW


Part 3: Launch EC2 Instances in Public and Private Subnets

Step 1️⃣ – Go to EC2 Launch Wizard

1. AWS Console → EC2 → Launch Instances
2. Click Launch Instances

Step 2️⃣ – Choose AMI

Amazon Linux 2023 (or any preferred AMI)
Click Next: Configure Instance Details

Step 3️⃣ – Configure Instance Details
Public Subnet Instance
Network: DemoVPC
Subnet: PublicSubnet
Auto-assign Public IP: Enabled
Private Subnet Instance
Network: DemoVPC
Subnet: PrivateSubnet
Auto-assign Public IP: Disabled


Step 4️⃣ – Add Storage

Leave default (8 GB) or adjust as needed
Click Next → Add Tags


Step 5️⃣ – Add Tags

Key	Value
Name	PublicInstance
Name	PrivateInstance


Step 6️⃣ – Configure Security Groups

Public Instance
Security Group → Allow SSH from My IP


Private Instance
Security Group → No inbound SSH from Internet
Outbound → Allow all (default) → Access Internet via NAT Gateway


Step 7️⃣ – Review and Launch

1. Review all settings → Click Launch
2. Select/create Key Pair → Download .pem
3. Click Launch Instances


Step 8️⃣ – Verify Instances

Instance	Subnet	Public IP	Private IP
PublicInstance	PublicSubnet	✅ Assigned	10.0.1.x
PrivateInstance	PrivateSubnet	❌ None	10.0.2.x


Step 9️⃣ – Test Connectivity

Public EC2
SSH using public IP → succeeds
Internet access works directly

Private EC2
SSH from Internet → fails
Internet access works via NAT Gateway (outbound only)


Key Notes
Private instances require NAT Gateway for outbound Internet
Public instances require IGW + Auto-assign Public IP
Security Groups + Route Tables control all traffic flow



✅ Result
Public subnet → Internet-accessible
Private subnet → Outbound Internet via NAT, inbound blocked
Demonstrates subnet isolation, routing, and NAT use





