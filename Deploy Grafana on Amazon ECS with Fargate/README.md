# Deploy Grafana on Amazon ECS with Fargate

## 🎯 Goal
Deploy a containerized Grafana instance on AWS ECS Fargate for monitoring and visualization, accessible via a public IP address.

## 🛠️ Tech Stack
- **AWS Services:**
  - Amazon ECS (Elastic Container Service)
  - AWS Fargate (Serverless compute for containers)
  - Amazon VPC (Virtual Private Cloud)
  - Security Groups
- **Container:**
  - Grafana (Official Docker image)
- **Networking:**
  - Public IP addressing
  - Custom TCP port configuration

## 📋 Architecture
- ECS Cluster running on Fargate launch type
- Grafana container exposed on port 3000
- Security group configured for public access
- VPC networking for secure communication

## 🚀 Setup/Deployment Instructions

### Prerequisites
- AWS Account with ECS and VPC permissions
- Basic understanding of containerization

### Deployment Steps

1. **Create ECS Cluster**
   - Navigate to AWS ECS Console
   - Create a new cluster with Fargate launch type
   - Name: `grafana-cluster` (or your preferred name)

2. **Configure Security Group**
   - Create security group: `grafana-sg`
   - Add inbound rule:
     - Type: Custom TCP
     - Port: 3000
     - Source: 0.0.0.0/0 (or restrict to specific IPs for production)

3. **Create Task Definition**
   - Use Grafana official Docker image
   - Configure container port: 3000
   - Allocate appropriate CPU and memory (e.g., 0.5 vCPU, 1GB RAM)

4. **Create ECS Service**
   - Launch type: Fargate
   - Desired tasks: 1
   - Assign public IP: Enabled
   - Select your VPC and subnet
   - Attach the `grafana-sg` security group

5. **Access Grafana**
   - Locate the public IP of your running task
   - Open browser and navigate to: `http://<public-ip>:3000`
   - Default credentials: admin/admin

### Important Notes
- For production environments, use a load balancer instead of direct public IP
- Restrict security group access to known IP addresses
- Configure persistent storage for Grafana data using EFS or RDS
- Set up HTTPS with SSL/TLS certificates for secure access

## 📊 CI/CD Status
*Note: CI/CD pipeline can be set up using GitHub Actions. Create a workflow to build and push Docker images to ECR, then update the ECS service automatically.*

Example badge (once workflow is set up):
```markdown
![Deploy Status](https://github.com/Tyjay00/AWS-Projects/workflows/Deploy-Grafana-ECS/badge.svg)
```

## 📖 Documentation
For detailed step-by-step instructions with screenshots, see [Deploy Grafana on Amazon ECS with Fargate.md](Deploy%20Grafana%20on%20Amazon%20ECS%20with%20Fargate.md).

## 💡 Benefits
- **Serverless**: No server management with Fargate
- **Scalable**: Easily scale tasks based on demand
- **Quick Setup**: Deploy in minutes without managing infrastructure
- **Cost-Effective**: Pay only for resources used
