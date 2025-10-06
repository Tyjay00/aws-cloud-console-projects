# Launching and Securing a Windows EC2 Instance with RDP Access

## 🎯 Goal
Deploy a Windows Server 2022 EC2 instance in a public subnet with secure Remote Desktop Protocol (RDP) access restricted to a specific IP address.

## 🛠️ Tech Stack
- **AWS Services:**
  - Amazon EC2 (Elastic Compute Cloud)
  - Amazon VPC (Virtual Private Cloud)
  - Security Groups
  - Key Pairs (for password decryption)
- **Operating System:**
  - Windows Server 2022 Base
- **Remote Access:**
  - Remote Desktop Protocol (RDP)
  - Port 3389

## 📋 Architecture
- EC2 Windows Server instance in a public subnet
- Security group with RDP access restricted to specific IP
- Internet Gateway for public internet access
- Key pair for secure password retrieval
- Public IP address for remote connectivity

## 🚀 Setup/Deployment Instructions

### Prerequisites
- AWS Account with EC2 permissions
- Your public IP address
- RDP client installed (Windows Remote Desktop, Microsoft Remote Desktop for Mac, Remmina for Linux)

### Deployment Steps

1. **Navigate to EC2 Dashboard**
   - Log in to AWS Management Console
   - Search for "EC2" and select the service

2. **Launch New Instance**
   - Click "Launch instance"
   - Provide instance name

3. **Choose Amazon Machine Image (AMI)**
   - Select: Windows Server 2022 Base
   - Architecture: 64-bit (x86)

4. **Select Instance Type**
   - Recommended: `t2.micro` (Free tier eligible)
   - Or choose based on your workload requirements

5. **Configure Key Pair**
   - Create new key pair or select existing one
   - Download `.pem` file (save securely)
   - This is used to decrypt Windows administrator password

6. **Network Settings**
   - VPC: Select your VPC (or use default)
   - Subnet: Choose a public subnet
   - Auto-assign Public IP: Enable

7. **Configure Security Group**
   - Create new security group: `windows-rdp-sg`
   - Add inbound rule:
     - Type: RDP
     - Protocol: TCP
     - Port: 3389
     - Source: My IP (or specific IP address)
     - Description: "RDP access from trusted IP"
   
   **Important**: Never use `0.0.0.0/0` for RDP access in production!

8. **Storage Configuration**
   - Default: 30 GB gp3 root volume
   - Adjust based on requirements

9. **Launch Instance**
   - Review settings and launch
   - Wait for instance state: "Running"

### Connecting via RDP

1. **Get Windows Password**
   - Select your instance in EC2 console
   - Click "Connect" → "RDP client" tab
   - Click "Get password"
   - Upload your `.pem` key pair file
   - Click "Decrypt password"
   - Save the password securely

2. **Connect Using RDP Client**
   - Open Remote Desktop Connection
   - Enter the public IP address
   - Username: `Administrator`
   - Password: (decrypted password from step 1)
   - Accept security certificate warning

3. **Verify Connection**
   - You should see Windows Server desktop
   - Configure Windows as needed

### Security Best Practices
- Always restrict RDP to specific IP addresses
- Use strong passwords and rotate regularly
- Consider using AWS Systems Manager Session Manager as alternative to RDP
- Enable Multi-Factor Authentication (MFA) where possible
- Keep Windows Server updated with latest patches
- Use AWS Security Hub for continuous security monitoring

## 📊 CI/CD Status
*Note: For infrastructure as code deployments, consider using:*
- AWS CloudFormation or Terraform for automated EC2 provisioning
- GitHub Actions for automated deployments

Example status badge (once workflow is set up):
```markdown
![Deploy Status](https://github.com/Tyjay00/AWS-Projects/workflows/Deploy-Windows-EC2/badge.svg)
```

## 📖 Documentation
For detailed step-by-step instructions with screenshots, see [EC2 Windows Server Deployment.md](EC2%20Windows%20Server%20Deployment.md).

## 💡 Benefits
- **Secure Access**: IP-restricted RDP prevents unauthorized access
- **Quick Setup**: Deploy Windows Server in minutes
- **Scalable**: Easily change instance types based on workload
- **Cost-Effective**: Use t2.micro for free tier or pay-as-you-go
- **Flexible**: Full Windows Server environment for any application

## 🔒 Security Considerations
- Regularly update security group rules
- Monitor CloudTrail logs for unauthorized access attempts
- Use AWS Systems Manager for patch management
- Enable CloudWatch monitoring for instance metrics
- Consider implementing a VPN or AWS Client VPN for enhanced security
