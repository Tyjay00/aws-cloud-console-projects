# AWS Projects Portfolio

Welcome to my AWS Projects Portfolio! This repository showcases practical, hands-on AWS implementations covering serverless architectures, container orchestration, networking, and infrastructure automation.

## 📚 Projects Overview

### 1. [FinMate: AI Chatbot & Serverless Integration](./AI%20Chatbot%20%26%20Serverless%20Integration/)
**Goal:** Web-based financial hub with live market data and AI-powered chatbot using serverless microservices architecture.

**Tech Stack:** AWS Lambda, API Gateway, HTML/CSS/JavaScript, Finnhub API

**Highlights:**
- Serverless microservices with dedicated Lambda functions
- Real-time market data integration
- 24/7 AI chatbot for customer support
- [Live Demo](https://fnbappacademy.tyrone.studio/chatbot-lambda.html)

---

### 2. [Deploy Grafana on Amazon ECS with Fargate](./Deploy%20Grafana%20on%20Amazon%20ECS%20with%20Fargate/)
**Goal:** Deploy containerized Grafana for monitoring and visualization on AWS ECS Fargate.

**Tech Stack:** Amazon ECS, AWS Fargate, Grafana, VPC, Security Groups

**Highlights:**
- Serverless container deployment
- Public IP access with custom security groups
- No infrastructure management required

---

### 3. [Deploying Metabase on AWS ECS with RDS PostgreSQL](./Deploying%20Metabase%20on%20AWS%20ECS%20with%20RDS%20PostgreSQL/)
**Goal:** Production-ready Metabase business intelligence deployment with managed PostgreSQL database.

**Tech Stack:** Amazon ECS, AWS Fargate, Amazon RDS (PostgreSQL), Application Load Balancer

**Highlights:**
- High availability with ALB
- Secure database in private subnet
- Multi-tier architecture
- Health checks and auto-scaling ready

---

### 4. [Serverless Image Processing Pipeline](./Serverless%20Image%20Processing%20Pipeline/)
**Goal:** Automated image processing pipeline using AWS Rekognition for label detection and watermarking.

**Tech Stack:** AWS Lambda (Python), S3, AWS Rekognition, Pillow, Docker

**Highlights:**
- Event-driven architecture
- AI-powered image analysis
- Fully automated processing
- Custom Lambda layers for dependencies

---

### 5. [Launching and Securing a Windows EC2 Instance with RDP Access](./Launching%20and%20Securing%20a%20Windows%20EC2%20Instance%20with%20RDP%20Access/)
**Goal:** Deploy Windows Server 2022 EC2 instance with secure, IP-restricted RDP access.

**Tech Stack:** Amazon EC2, Windows Server 2022, Security Groups, RDP

**Highlights:**
- IP-restricted security configuration
- Secure remote access setup
- Key pair authentication
- Best practices for Windows on AWS

---

### 6. [Multi-VPC Setup and Peering](./Multi-VPC%20Setup%20and%20Peering/)
**Goal:** Create isolated Development and Production VPCs with secure peering connection for inter-VPC communication.

**Tech Stack:** Amazon VPC, VPC Peering, Internet Gateway, Route Tables, Subnets

**Highlights:**
- Environment isolation (Dev/Prod)
- Custom networking configuration
- Secure cross-VPC communication
- Non-overlapping CIDR design

---

## 🛠️ Technologies Used

**AWS Services:**
- Compute: EC2, ECS, Fargate, Lambda
- Storage: S3
- Database: RDS (PostgreSQL)
- Networking: VPC, Subnets, Internet Gateway, ALB, Security Groups, VPC Peering
- AI/ML: Rekognition
- Management: IAM, CloudWatch

**Development Tools & Languages:**
- Python, JavaScript, HTML/CSS
- Docker
- Boto3 (AWS SDK for Python)
- Pillow (Image Processing)

---

## 📊 CI/CD & Best Practices

Each project in this repository can be enhanced with CI/CD pipelines using **GitHub Actions**. While the workflows are not yet implemented, here's what can be added:

### Example CI/CD Workflow Structure:
```yaml
name: Deploy to AWS
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
      - name: Configure AWS credentials
      - name: Deploy to AWS
      - name: Run tests
```

### Status Badges
Once GitHub Actions workflows are configured, you can add status badges to monitor build and deployment status:

```markdown
![Build Status](https://github.com/Tyjay00/AWS-Projects/workflows/workflow-name/badge.svg)
```

---

## 🚀 Getting Started

Each project directory contains:
- **README.md**: Quick start guide with goal, tech stack, and deployment instructions
- **Detailed Documentation**: Step-by-step guides with screenshots and explanations

To explore a project:
1. Click on the project link above
2. Review the README.md for overview
3. Follow the deployment instructions
4. Check detailed documentation for in-depth guidance

---

## 📖 Learning Objectives

This portfolio demonstrates proficiency in:
- **Serverless Architecture**: Lambda functions, event-driven design
- **Container Orchestration**: ECS, Fargate, Docker
- **Database Management**: RDS configuration, security best practices
- **Network Engineering**: VPC design, subnets, routing, peering
- **Security**: IAM roles, security groups, least-privilege access
- **DevOps Practices**: Infrastructure as Code concepts, CI/CD readiness
- **Cost Optimization**: Serverless and pay-as-you-go architectures

---

## 💡 Key Takeaways

- **Serverless-First Approach**: Leverage managed services to reduce operational overhead
- **Security by Design**: Implement least-privilege access and network isolation
- **Scalability**: Build architectures that automatically scale with demand
- **Cost Efficiency**: Use serverless and managed services to optimize costs
- **Documentation**: Maintain clear, comprehensive documentation for all projects

---

## 🤝 Contributing

Feel free to explore, learn from, and adapt these projects for your own use. If you have suggestions or improvements, please open an issue or submit a pull request.

---

## 📞 Contact

For questions or collaboration opportunities, please reach out via GitHub.

---

## 📄 License

This repository is for educational and portfolio purposes. Please refer to individual project documentation for specific usage guidelines.

---

**Happy Learning and Building! 🚀**