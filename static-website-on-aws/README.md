# 🌐 Static Website on AWS

A production-ready static website hosting solution using AWS services for global content delivery, security, and scalability.

[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazon-aws)](https://aws.amazon.com/)
[![S3](https://img.shields.io/badge/Storage-S3-569A31?logo=amazon-s3)](https://aws.amazon.com/s3/)
[![CloudFront](https://img.shields.io/badge/CDN-CloudFront-FF9900?logo=amazon-aws)](https://aws.amazon.com/cloudfront/)
[![HTTPS](https://img.shields.io/badge/Security-HTTPS-brightgreen?logo=let's-encrypt)](https://letsencrypt.org/)

## 🎯 Project Overview

This project demonstrates how to deploy a secure, scalable, and cost-effective static website on AWS using modern cloud architecture patterns. Perfect for portfolios, documentation sites, marketing pages, and any static web content that needs global reach and enterprise-grade security.

## 🏗️ Architecture

```
┌─────────────┐    ┌──────────────┐    ┌─────────────┐    ┌──────────────┐
│   Route53   │───▶│  CloudFront  │───▶│  S3 Bucket  │    │ Certificate  │
│ DNS & Domain│    │ Global CDN   │    │Static Files │    │   Manager    │
└─────────────┘    └──────────────┘    └─────────────┘    └──────────────┘
       │                    │                   │                   │
       │                    │                   │                   │
   Custom Domain      HTTPS Delivery     Website Files        SSL/TLS
   Management         & Caching          Storage               Certificate
```

### Key Components

- **🗄️ Amazon S3**: Static file hosting with public read access
- **🌍 CloudFront**: Global CDN for fast content delivery worldwide
- **🌐 Route53**: DNS management with custom domain configuration
- **🔒 Certificate Manager**: Free SSL/TLS certificates for HTTPS
- **🛡️ Security**: Comprehensive security policies and best practices

## ✨ Features

### 🚀 Performance
- **Global CDN**: Sub-100ms response times worldwide
- **Edge Caching**: Intelligent content caching at 400+ edge locations
- **Compression**: Automatic Gzip compression for faster loading
- **HTTP/2**: Modern protocol support for improved performance

### 🔒 Security
- **HTTPS Everywhere**: Enforced SSL/TLS encryption
- **Security Headers**: HSTS, CSP, and other security headers
- **Access Control**: Least-privilege bucket policies
- **DDoS Protection**: Built-in AWS Shield Standard protection

### 💰 Cost Optimization
- **Pay-as-you-go**: No upfront costs or minimum commitments
- **Free Tier**: Generous AWS free tier allowances
- **Storage Classes**: Intelligent tiering for cost optimization
- **Bandwidth**: CloudFront's cost-effective global delivery

### 📊 Monitoring & Analytics
- **CloudWatch Integration**: Real-time metrics and monitoring
- **Access Logs**: Detailed request logging and analysis
- **Performance Insights**: Cache hit ratios and response times
- **Cost Tracking**: Detailed billing and usage reports

## 🎯 Use Cases

### Perfect For:
- **👨‍💼 Portfolio Websites**: Showcase your work globally
- **📚 Documentation Sites**: Technical documentation with fast search
- **🏢 Corporate Landing Pages**: Marketing and informational sites
- **📱 Single Page Applications**: React, Vue, Angular deployments
- **🎨 Creative Showcases**: Photography, design, and art portfolios

### Business Benefits:
- **⚡ 99.99% Availability**: Enterprise-grade uptime SLA
- **🌍 Global Reach**: Serve content from 400+ edge locations
- **📈 Auto-Scaling**: Handles traffic spikes automatically
- **🔧 Zero Maintenance**: Fully managed infrastructure

## 🚀 Quick Start

### Prerequisites
- AWS Account with appropriate permissions
- Domain name (optional, for custom domain)
- AWS CLI configured
- Basic understanding of AWS services

### 🏃‍♂️ Fast Track Deployment

1. **Clone the repository**
   ```bash
   git clone https://github.com/Tyjay00/aws-cloud-console-projects.git
   cd aws-cloud-console-projects/static-website-on-aws
   ```

2. **Review the architecture**
   - Check `docs/SETUP.md` for detailed instructions
   - Examine `example_site/` for website structure
   - Review `templates/` for configuration files

3. **Deploy your website**
   - Follow the step-by-step guide in `docs/SETUP.md`
   - Use provided templates for quick setup
   - Customize for your specific requirements

### 📖 Detailed Documentation

For complete setup instructions with screenshots and troubleshooting:

**📋 [Complete Setup Guide](docs/SETUP.md)**

## 📁 Project Structure

```
static-website-on-aws/
├── 📄 README.md                    # Project overview (this file)
├── 📁 docs/
│   ├── 📋 SETUP.md                 # Detailed setup instructions
│   └── 📁 images/                  # Step-by-step screenshots
├── 📁 example_site/
│   └── 🌐 index.html              # Sample website files
└── 📁 templates/
    └── 🔧 s3-bucket-policy.json   # AWS policy templates
```

## 💻 Example Implementation

### Sample Website Structure
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My AWS Static Website</title>
</head>
<body>
    <h1>Welcome to My AWS-Hosted Website</h1>
    <p>Powered by S3, CloudFront, and Route53</p>
</body>
</html>
```

### S3 Bucket Policy Template
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::your-bucket-name/*"
        }
    ]
}
```

## 💰 Cost Estimation

### Monthly Cost Breakdown (Example)
| Service | Usage | Estimated Cost |
|---------|-------|----------------|
| **S3 Storage** | 1GB static files | $0.02 |
| **CloudFront** | 10GB data transfer | $0.85 |
| **Route53** | 1 hosted zone | $0.50 |
| **Certificate Manager** | SSL certificate | Free |
| **Total** | Small-medium site | **~$1.37/month** |

*Costs vary based on traffic and storage requirements. Many components are covered by AWS Free Tier.*

## 🔧 Advanced Features

### Performance Optimization
- **Image Optimization**: WebP format support
- **Lazy Loading**: Progressive content loading
- **Cache Strategies**: Optimized TTL settings
- **Minification**: CSS/JS compression

### Security Enhancements
- **WAF Integration**: Web application firewall
- **Security Headers**: HSTS, CSP configuration
- **Access Logging**: Comprehensive audit trails
- **Origin Access Identity**: Secure S3 access

## 🔍 Troubleshooting

### Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| **Certificate validation fails** | Verify DNS records and domain ownership |
| **CloudFront not updating** | Create cache invalidation |
| **Custom domain not working** | Check Route53 and CloudFront configuration |
| **HTTPS redirect issues** | Review CloudFront viewer protocol policy |

📖 **For detailed troubleshooting**: See [SETUP.md](docs/SETUP.md#troubleshooting)

## 📊 Monitoring & Maintenance

### Key Metrics to Monitor
- **Cache Hit Ratio**: Target >85% for optimal performance
- **Origin Response Time**: Should be <200ms
- **Error Rates**: Monitor 4xx and 5xx responses
- **Data Transfer**: Track bandwidth usage and costs

### Regular Maintenance Tasks
- [ ] Review and rotate SSL certificates (automated)
- [ ] Monitor CloudWatch metrics and set up alerts
- [ ] Review access logs for security insights
- [ ] Update content and test deployment pipeline
- [ ] Optimize cache policies based on usage patterns

## 🎓 Learning Outcomes

After completing this project, you'll understand:

### AWS Services
- **S3**: Static website hosting and bucket policies
- **CloudFront**: CDN configuration and optimization
- **Route53**: DNS management and domain configuration
- **Certificate Manager**: SSL/TLS certificate management

### Best Practices
- **Security**: Implementing least-privilege access
- **Performance**: CDN optimization and caching strategies
- **Cost**: Resource optimization and monitoring
- **Operations**: Deployment and maintenance procedures

## 🤝 Contributing

We welcome contributions! Here's how you can help:

### Ways to Contribute
- 🐛 **Report Issues**: Found a bug? Create an issue
- 💡 **Suggest Features**: Have an idea? Share it with us
- 📝 **Improve Documentation**: Help make guides clearer
- 🔧 **Code Contributions**: Submit pull requests
- ⭐ **Spread the Word**: Star the repo and share

### Development Workflow
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📚 Additional Resources

### AWS Documentation
- [Amazon S3 Static Website Hosting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)
- [CloudFront Developer Guide](https://docs.aws.amazon.com/cloudfront/latest/developerguide/)
- [Route53 Developer Guide](https://docs.aws.amazon.com/route53/latest/developerguide/)
- [Certificate Manager User Guide](https://docs.aws.amazon.com/acm/latest/userguide/)

### Related Projects
- [🤖 FinMate AI Chatbot](../AI%20Chatbot%20%26%20Serverless%20Integration/)
- [📊 Grafana on ECS](../Deploy%20Grafana%20on%20Amazon%20ECS%20with%20Fargate/)
- [🌐 Multi-VPC Architecture](../Multi-VPC%20Setup%20and%20Peering/)

## 📞 Support

### Getting Help
- **📖 Documentation**: Check [SETUP.md](docs/SETUP.md) first
- **🐛 Issues**: Create a GitHub issue for bugs
- **💬 Discussions**: Use GitHub Discussions for questions
- **📧 Contact**: Reach out to project maintainers

### Community
- **GitHub**: [@Tyjay00](https://github.com/Tyjay00)
- **LinkedIn**: [Professional Profile](https://linkedin.com/in/yourprofile)
- **Portfolio**: [Complete AWS Projects](https://github.com/Tyjay00/aws-cloud-console-projects)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](../LICENSE) file for details.

### Usage Rights
- ✅ **Educational Use**: Perfect for learning AWS
- ✅ **Commercial Use**: Use in your business projects
- ✅ **Modification**: Adapt to your specific needs
- ✅ **Distribution**: Share with your team and community

---

<div align="center">

**🌟 Star this repository if it helped you!**

[![GitHub stars](https://img.shields.io/github/stars/Tyjay00/aws-cloud-console-projects?style=social)](https://github.com/Tyjay00/aws-cloud-console-projects)
[![GitHub forks](https://img.shields.io/github/forks/Tyjay00/aws-cloud-console-projects?style=social)](https://github.com/Tyjay00/aws-cloud-console-projects)

**🚀 Built with ❤️ for the AWS Community**

[🎯 More AWS Projects](https://github.com/Tyjay00/aws-cloud-console-projects) | [☁️ Infrastructure Modules](https://github.com/Tyjay00/terraform-aws-infrastructure-modules) | [🔗 Full Portfolio](https://github.com/Tyjay00)

</div>
