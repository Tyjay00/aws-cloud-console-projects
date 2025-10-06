# Metabase Deployment on AWS ECS Fargate with RDS PostgreSQL

## 🎯 Goal
Deploy Metabase, an open-source business intelligence tool, on AWS ECS Fargate with a managed PostgreSQL database (RDS) for production-ready data analytics and visualization.

## 🛠️ Tech Stack
- **AWS Services:**
  - Amazon ECS (Elastic Container Service)
  - AWS Fargate (Serverless container compute)
  - Amazon RDS for PostgreSQL (Managed database)
  - Application Load Balancer (ALB)
  - Amazon VPC (Virtual Private Cloud)
  - Security Groups
- **Application:**
  - Metabase (Open-source BI tool)
- **Database:**
  - PostgreSQL (RDS managed instance)

## 📋 Architecture
- ECS Fargate cluster running Metabase containers
- RDS PostgreSQL for Metabase application data
- Application Load Balancer for traffic distribution and high availability
- Multi-subnet VPC architecture for security and resilience
- Security groups configured for least-privilege access

## 🚀 Setup/Deployment Instructions

### Prerequisites
- AWS Account with appropriate permissions
- Understanding of VPC networking and security groups

### Deployment Steps

1. **Create RDS PostgreSQL Instance**
   - Navigate to Amazon RDS
   - Create PostgreSQL database: `metabase-db`
   - Configure as **not publicly accessible**
   - Note database endpoint, username, and password

2. **Set Up Application Load Balancer**
   - Create internet-facing ALB: `metabase-lb`
   - Configure listener on port 80 (HTTP)
   - Create target group: `metabase-tg`
     - Target type: IP addresses
     - Port: 3000
     - Health check: `/api/health`

3. **Configure Security Groups**
   - ALB Security Group:
     - Inbound: Allow HTTP (80) from 0.0.0.0/0
     - Outbound: Allow traffic to ECS tasks on port 3000
   - ECS Security Group:
     - Inbound: Allow port 3000 from ALB security group
     - Outbound: Allow port 5432 to RDS security group
   - RDS Security Group:
     - Inbound: Allow port 5432 from ECS security group

4. **Create ECS Cluster and Task Definition**
   - Create Fargate cluster: `metabase-cluster`
   - Create task definition with:
     - Metabase container image
     - Environment variables:
       ```
       MB_DB_TYPE=postgres
       MB_DB_HOST=<rds-endpoint>
       MB_DB_PORT=5432
       MB_DB_NAME=<database-name>
       MB_DB_USER=<username>
       MB_DB_PASS=<password>
       ```
     - Port mapping: 3000

5. **Launch ECS Service**
   - Desired tasks: 1 (or more for HA)
   - Launch type: Fargate
   - Attach to ALB target group
   - Enable auto-scaling (optional)

6. **Access Metabase**
   - Navigate to ALB DNS name in browser
   - Complete Metabase setup wizard
   - Connect to your data sources

### Security Best Practices
- Keep RDS instance in private subnets
- Use AWS Secrets Manager for database credentials
- Enable HTTPS on ALB with SSL/TLS certificate
- Restrict ALB access to known IPs if possible
- Enable VPC Flow Logs for monitoring

## 📊 CI/CD Status
*Note: Implement CI/CD using GitHub Actions to automate container builds and ECS deployments. Example workflow:*

```yaml
name: Deploy Metabase to ECS
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to ECS
        # Add deployment steps
```

Once configured, add status badge:
```markdown
![Build Status](https://github.com/Tyjay00/AWS-Projects/workflows/Deploy-Metabase/badge.svg)
```

## 📖 Documentation
For detailed deployment steps with screenshots and troubleshooting, see [Deploy Metabase with RDS.md](Deploy%20Metabase%20with%20RDS.md).

## 💡 Benefits
- **Managed Infrastructure**: No server management with Fargate and RDS
- **High Availability**: Load balancer distributes traffic across multiple tasks
- **Secure**: Private database with network isolation
- **Scalable**: Easily scale ECS tasks based on demand
- **Production-Ready**: Persistent database storage for business-critical data
