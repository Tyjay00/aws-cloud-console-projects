# 🌐 Static Website on AWS - Complete Setup Guide

This guide walks you through setting up a secure, scalable static website on AWS using S3, CloudFront, Route53, and Certificate Manager.

## 📋 Architecture Overview

This project deploys a complete static website hosting solution with:
- **S3 Bucket**: Static file hosting with public read access
- **CloudFront**: Global CDN for fast content delivery
- **Route53**: DNS management with custom domain
- **Certificate Manager**: SSL/TLS certificates for HTTPS
- **Security**: Secure bucket policies and HTTPS enforcement

## 🎯 Prerequisites

- AWS Account with appropriate permissions
- Domain name registered (for custom domain setup)
- AWS CLI configured
- Basic understanding of AWS services

## 🚀 Step-by-Step Implementation

### Step 1: Create and Configure S3 Bucket

1. **Create an S3 bucket** for static website hosting
2. **Enable static website hosting** in bucket properties
3. **Configure public read access** with appropriate bucket policy

![S3 Bucket Configuration](images/S3%20Bucket%20Configuration%20Static%20website%20hosting%20enable%20-%20Copy.png)

**Configure the bucket policy for public read access:**

![S3 Bucket Policy](images/S3%20Bucket%20Policy%20Confirmation%20of%20Public%20Read%20Access%20-%20Copy.png)

### Step 2: SSL Certificate Setup

1. **Request SSL certificate** in AWS Certificate Manager
2. **Validate the certificate** using DNS validation
3. **Confirm certificate status** as "Issued"

![Certificate Manager SSL Certificate](images/Certificate%20Manager%20Validated%20SSL%20Certificate%20-%20Copy.png)

**Certificate details and validation:**

![Certificate Details](images/Certificate%20Details%20-%20Copy.png)

### Step 3: CloudFront Distribution Setup

1. **Create CloudFront distribution** with S3 origin
2. **Configure custom domain** and SSL certificate
3. **Wait for deployment** to complete

![CloudFront Distribution Settings](images/CloudFront%20Distribution%20Distribution%20settings%20-%20Copy.png)

**CloudFront deployment status:**

![CloudFront Deployed Status](images/CloudFront%20Distribution%20Deployed%20status%20-%20Copy.png)

**CloudFront settings with custom domain and certificate:**

![CloudFront Custom Domain](images/CloudFront%20Settings%20Showing%20the%20custom%20domain%20and%20certificate%20-%20Copy.png)

### Step 4: Route53 DNS Configuration

1. **Create hosted zone** for your domain
2. **Configure DNS records** to point to CloudFront
3. **Verify DNS propagation**

![Route53 Hosted Zone](images/Route53%20%20Hosted%20Zone%20-%20Copy.png)

### Step 5: Website Deployment and Testing

1. **Upload website files** to S3 bucket
2. **Test CloudFront URL** access
3. **Verify custom domain** functionality
4. **Confirm HTTPS** is working

**CloudFront distribution URL:**

![Site on CloudFront](images/site%20on%20cloudfront%20link%20-%20Copy.png)

**Website details and configuration:**

![Website Details](images/website%20details%20page%20-%20Copy.png)

**Final verification - Website loading over HTTPS:**

![Website HTTPS](images/Website%20loading%20over%20HTTPS%20-%20Copy.png)

## 🔧 Configuration Files

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

### Example Website Structure

```
example_site/
├── index.html          # Main landing page
├── css/               # Stylesheets
├── js/                # JavaScript files
├── images/            # Website images
└── assets/            # Other static assets
```

## 🛡️ Security Best Practices

### S3 Security
- ✅ Enable bucket encryption
- ✅ Use least-privilege bucket policies
- ✅ Block public ACLs (use bucket policy instead)
- ✅ Enable access logging

### CloudFront Security
- ✅ Force HTTPS redirects
- ✅ Use security headers
- ✅ Enable AWS WAF (if needed)
- ✅ Configure custom error pages

### Certificate Security
- ✅ Use DNS validation for certificates
- ✅ Enable automatic renewal
- ✅ Monitor certificate expiration

## 📊 Monitoring and Maintenance

### CloudWatch Metrics
- Monitor CloudFront performance
- Track S3 request metrics
- Set up billing alerts

### Regular Maintenance
- Review access logs
- Update security policies
- Monitor certificate expiration
- Test disaster recovery procedures

## 💰 Cost Optimization

### S3 Storage Classes
- Use appropriate storage classes for different content types
- Enable lifecycle policies for old content
- Consider S3 Intelligent Tiering

### CloudFront Optimization
- Configure appropriate cache behaviors
- Use compression for text files
- Monitor data transfer costs

## 🔍 Troubleshooting

### Common Issues

1. **Certificate validation fails**
   - Verify DNS records are correct
   - Check domain ownership
   - Ensure certificate is in us-east-1 region

2. **CloudFront not serving updated content**
   - Create cache invalidation
   - Check cache behaviors
   - Verify S3 object permissions

3. **Custom domain not accessible**
   - Verify Route53 configuration
   - Check DNS propagation
   - Confirm CloudFront alternate domain names

### Verification Commands

```powershell
# Check DNS resolution
nslookup your-domain.com

# Test HTTPS certificate
curl -I https://your-domain.com

# Verify CloudFront headers
curl -H "User-Agent: test" -I https://your-domain.com
```

## 🎯 Performance Optimization

### CloudFront Settings
- Configure appropriate cache policies
- Enable Gzip compression
- Use HTTP/2 support
- Set optimal TTL values

### S3 Optimization
- Use appropriate content types
- Optimize image sizes
- Implement lazy loading for images

## 📚 Additional Resources

- [AWS S3 Static Website Hosting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)
- [CloudFront Distribution Guide](https://docs.aws.amazon.com/cloudfront/latest/developerguide/)
- [Certificate Manager Documentation](https://docs.aws.amazon.com/acm/)
- [Route53 DNS Configuration](https://docs.aws.amazon.com/route53/)

## 🤝 Support

For issues or questions:
1. Check the troubleshooting section above
2. Review AWS documentation
3. Create an issue in the project repository
4. Contact project maintainers

---

**🎉 Congratulations!** You now have a fully functional, secure static website hosted on AWS with global CDN, custom domain, and SSL certificate.
