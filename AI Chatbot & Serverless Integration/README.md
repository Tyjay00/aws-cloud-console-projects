# FinMate: Financial Markets & Loan Hub

## 🎯 Goal
FinMate is a web-based financial hub that provides live market data, financial calculators, and an AI-powered chatbot for 24/7 customer support and personalized financial insights.

## 🔗 Live Demo
[FinMate Application](https://fnbappacademy.tyrone.studio/chatbot-lambda.html)

## 🛠️ Tech Stack
- **AWS Services:**
  - AWS Lambda (Serverless compute)
  - API Gateway
  - AWS IAM (Identity and Access Management)
- **Frontend:**
  - HTML5
  - CSS3
  - JavaScript
- **External APIs:**
  - Finnhub API (Market data)
  - Custom JSE Lambda function

## 📋 Architecture
The application uses a serverless microservices architecture with dedicated Lambda functions:
- `finmate-chatbot` - AI-powered conversational assistant
- `finmate-jse-index` - JSE All Share Index data fetcher
- `finmate-us-stocks` - US stock market data
- `finmate-exchange-rates` - Currency exchange rates

## 🚀 Setup/Deployment Instructions

### Prerequisites
- AWS Account with appropriate permissions
- Finnhub API key

### Configuration Steps
1. Clone the repository
2. Update `index.html` with your API credentials:
   ```javascript
   const FINNHUB_API_KEY = "YOUR_FINNHUB_API_KEY_HERE";
   const JSE_LAMBDA_URL = "YOUR_JSE_LAMBDA_FUNCTION_URL_HERE";
   ```
3. Deploy Lambda functions:
   - Create Lambda functions for each microservice
   - Configure API Gateway endpoints
   - Set up proper IAM roles and permissions
4. Host the frontend files on your web server or AWS S3 + CloudFront
5. Configure data refresh rate (default: 5 minutes)

### Key Features
- Real-time market data from US and JSE exchanges
- Currency exchange rates
- Financial calculators (loan, investment)
- AI chatbot for customer engagement
- Auto-refresh functionality to stay within API rate limits

## 📊 CI/CD Status
*Note: CI/CD pipeline can be set up using GitHub Actions for automated testing and deployment. Add a workflow file in `.github/workflows/` to enable automated builds and deployments.*

## 📖 Documentation
For detailed architecture and business integration information, see [Finmate Project Documentation.md](Finmate%20Project%20Documentation.md).

## 💡 Key Benefits
- **Cost-Effective**: Serverless architecture means you only pay for actual usage
- **Scalable**: Automatically handles traffic spikes
- **Maintainable**: Independent microservices for easy updates
- **24/7 Availability**: Chatbot provides round-the-clock customer support
