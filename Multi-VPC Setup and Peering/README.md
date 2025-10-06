# Multi-VPC Setup and Peering

## 🎯 Goal
Create a multi-VPC architecture with separate Development and Production environments, configure networking components, and establish VPC peering for secure inter-VPC communication.

## 🛠️ Tech Stack
- **AWS Services:**
  - Amazon VPC (Virtual Private Cloud)
  - VPC Peering Connection
  - Internet Gateway (IGW)
  - Route Tables
  - Subnets (Public and Private)
- **Networking:**
  - CIDR blocks and IP addressing
  - Routing and network isolation

## 📋 Architecture
- **Development VPC** (`10.0.0.0/16`)
  - Public Subnet: `10.0.1.0/24`
  - Private Subnet: `10.0.2.0/24`
  - Internet Gateway attached
  - Custom route tables
  
- **Production VPC** (`172.16.0.0/16`)
  - Public Subnet: `172.16.1.0/24`
  - Private Subnet: `172.16.2.0/24`
  - Internet Gateway attached
  - Custom route tables

- **VPC Peering Connection**
  - Enables private communication between VPCs
  - Routes configured for cross-VPC traffic

## 🚀 Setup/Deployment Instructions

### Prerequisites
- AWS Account with VPC permissions
- Understanding of CIDR notation and routing
- Knowledge of network security concepts

### Deployment Steps

#### 1. Create VPCs

**Development VPC:**
```
Name: Development VPC
IPv4 CIDR: 10.0.0.0/16
```

**Production VPC:**
```
Name: Production VPC
IPv4 CIDR: 172.16.0.0/16
```

#### 2. Create Subnets

**Development VPC Subnets:**
- Public Subnet: `10.0.1.0/24`
- Private Subnet: `10.0.2.0/24`

**Production VPC Subnets:**
- Public Subnet: `172.16.1.0/24`
- Private Subnet: `172.16.2.0/24`

Assign appropriate Availability Zones to each subnet.

#### 3. Attach Internet Gateways

For each VPC:
1. Create a new Internet Gateway
2. Attach to the respective VPC
3. Name appropriately (e.g., `dev-igw`, `prod-igw`)

#### 4. Configure Route Tables

**For each VPC, create route tables:**

**Public Route Table:**
- Associate with public subnet
- Add route: 
  - Destination: `0.0.0.0/0`
  - Target: Internet Gateway

**Private Route Table:**
- Associate with private subnet
- Add specific routes as needed (e.g., NAT Gateway for outbound traffic)

#### 5. Create VPC Peering Connection

1. **Request Peering Connection**
   - Requester: Development VPC
   - Accepter: Production VPC
   - Name: `dev-prod-peering`

2. **Accept Peering Connection**
   - Navigate to Production VPC
   - Accept the pending peering request

3. **Update Route Tables**
   
   **Development VPC Route Table:**
   - Destination: `172.16.0.0/16`
   - Target: Peering Connection

   **Production VPC Route Table:**
   - Destination: `10.0.0.0/16`
   - Target: Peering Connection

#### 6. Configure Security Groups

Update security groups to allow traffic from peered VPC CIDR blocks:

**Development VPC Security Group:**
- Inbound: Allow required ports from `172.16.0.0/16`

**Production VPC Security Group:**
- Inbound: Allow required ports from `10.0.0.0/16`

### Testing Connectivity

1. Launch EC2 instances in both VPCs
2. Ensure instances are in subnets with peering routes
3. Test connectivity using private IP addresses:
   ```bash
   ping <private-ip-of-instance-in-other-vpc>
   ```

### Important Considerations

- **Non-Overlapping CIDR Blocks**: VPCs must have different CIDR ranges
- **Transitive Peering**: Not supported - VPC A cannot reach VPC C through VPC B
- **DNS Resolution**: Enable DNS resolution for peering if hostname resolution is needed
- **Security Groups**: Must explicitly allow traffic from peered VPC CIDRs
- **Route Table Limits**: Each route table has limits on number of routes

## 📊 CI/CD Status
*Note: Infrastructure as Code using Terraform or CloudFormation is recommended for multi-VPC deployments.*

Example Terraform workflow with GitHub Actions:
```yaml
name: Deploy Multi-VPC Infrastructure
on:
  push:
    paths:
      - 'terraform/**'
jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - name: Terraform Apply
        # Add terraform deployment steps
```

Status badge (once workflow is configured):
```markdown
![Infrastructure Status](https://github.com/Tyjay00/AWS-Projects/workflows/Deploy-VPC/badge.svg)
```

## 📖 Documentation
For detailed step-by-step instructions with screenshots, see [Multi-VPC-Setup-and-Peering.md](Multi-VPC-Setup-and-Peering.md).

## 💡 Benefits
- **Environment Isolation**: Separate Dev and Prod workloads
- **Secure Communication**: Private connectivity between VPCs without internet
- **Flexible Networking**: Custom route tables for granular control
- **Scalable**: Easy to add more VPCs and peering connections
- **Cost-Effective**: No data transfer charges for VPC peering in same region

## 🔧 Use Cases
- Multi-environment deployments (Dev, Staging, Prod)
- Shared services architecture
- Partner integrations with separate VPCs
- Microservices with isolated network boundaries
- Disaster recovery across VPCs
