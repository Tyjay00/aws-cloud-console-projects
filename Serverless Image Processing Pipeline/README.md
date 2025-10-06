# Serverless Image Processing Pipeline

## 🎯 Goal
Build an automated serverless pipeline that detects labels in uploaded images using AWS Rekognition, adds watermarks with detected labels, and saves processed images to a destination S3 bucket.

## 🛠️ Tech Stack
- **AWS Services:**
  - AWS Lambda (Python 3.9)
  - Amazon S3 (Object storage)
  - AWS Rekognition (AI image analysis)
  - IAM (Identity and Access Management)
  - CloudWatch (Logging and monitoring)
- **Libraries:**
  - Pillow (PIL) - Image processing
  - Boto3 - AWS SDK for Python
- **Development Tools:**
  - Docker (for Lambda layer creation)

## 📋 Architecture
1. Image uploaded to source S3 bucket (`my-source-images-app`)
2. S3 triggers Lambda function automatically
3. Lambda function:
   - Calls AWS Rekognition to detect labels
   - Downloads the image
   - Adds watermark with detected labels using Pillow
   - Uploads processed image to destination bucket (`my-processed-images-app`)
4. Processed images stored with `watermarked-` prefix

## 🚀 Setup/Deployment Instructions

### Prerequisites
- AWS Account with Lambda, S3, and Rekognition permissions
- Docker installed (for creating Pillow Lambda layer)
- Basic Python knowledge

### Deployment Steps

1. **Create S3 Buckets**
   ```bash
   # Source bucket
   aws s3 mb s3://my-source-images-app
   
   # Destination bucket
   aws s3 mb s3://my-processed-images-app
   ```

2. **Create Lambda Layer for Pillow**
   ```bash
   mkdir python
   docker run --platform linux/x86_64 -v "$PWD":/var/task \
     "public.ecr.aws/lambda/python:3.9" \
     /bin/sh -c "pip install Pillow -t python; exit"
   zip -r PillowLayer.zip python
   ```
   
   Upload `PillowLayer.zip` to AWS Lambda Layers and attach to your function.

3. **Create Lambda Function**
   - Name: `ImageProcessorFunction`
   - Runtime: Python 3.9
   - Add Pillow layer created in step 2
   - Copy the Lambda code from documentation

4. **Configure Environment Variables**
   - Key: `PROCESSED_BUCKET`
   - Value: `my-processed-images-app`

5. **Set Up IAM Permissions**
   Attach policy to Lambda execution role:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "s3:GetObject",
           "s3:PutObject"
         ],
         "Resource": [
           "arn:aws:s3:::my-source-images-app/*",
           "arn:aws:s3:::my-processed-images-app/*"
         ]
       },
       {
         "Effect": "Allow",
         "Action": "rekognition:DetectLabels",
         "Resource": "*"
       }
     ]
   }
   ```

6. **Configure S3 Trigger**
   - On source bucket: `my-source-images-app`
   - Event type: All object create events
   - Filter: `.jpg`, `.jpeg`, `.png` files

7. **Adjust Lambda Settings**
   - Timeout: 30 seconds (or more for large images)
   - Memory: 512 MB (adjust based on image sizes)

### Testing
1. Upload an image to `my-source-images-app`
2. Check Lambda logs in CloudWatch
3. Verify watermarked image appears in `my-processed-images-app`

## 📊 CI/CD Status
*Note: GitHub Actions can automate Lambda deployments. Example workflow:*

```yaml
name: Deploy Lambda Function
on:
  push:
    paths:
      - 'Serverless Image Processing Pipeline/**'
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Lambda
        # Add deployment steps
```

Status badge (once workflow is configured):
```markdown
![Build Status](https://github.com/Tyjay00/AWS-Projects/workflows/Deploy-Lambda/badge.svg)
```

## 📖 Documentation
For detailed step-by-step guide with code and screenshots, see [Serverless Image Processing Pipeline Documentation.md](Serverless%20Image%20Processing%20Pipeline%20Documentation.md).

## 💡 Benefits
- **Fully Automated**: No manual intervention required
- **Serverless**: No infrastructure to manage
- **Scalable**: Handles any number of concurrent uploads
- **Cost-Effective**: Pay only for actual processing time
- **AI-Powered**: Leverages AWS Rekognition for intelligent label detection
- **Event-Driven**: Processes images immediately upon upload

## 🔧 Troubleshooting
- Ensure Lambda has appropriate IAM permissions
- Check CloudWatch logs for detailed error messages
- Verify environment variables are set correctly
- Confirm Pillow layer is attached to the function
