# FinMate: Financial Markets & Loan Hub

## 🔗 Check out the live site here: [FinMate](https://fnbappacademy.tyrone.studio/)

This document provides a comprehensive overview of the Finmate application, its features, technology stack, and setup instructions.

Finmate is a web-based financial hub that offers live market data, financial calculators, and an AI chatbot. 

## Its architecture is built on **serverless microservices** using **AWS Lambda** functions to ensure scalability and cost-effectiveness.

<img width="1788" height="803" alt="image" src="https://github.com/user-attachments/assets/d9feff3b-cabb-47ee-9369-be9085b91089" />

----
### Serverless Architecture & Business Integration

The core of Finmate's functionality relies on a modern, serverless microservices architecture. This approach, using AWS Lambda functions, provides significant benefits in terms of scalability, maintenance, and cost.

#### The AI Chatbot (finmate-chatbot)

The chatbot is more than just a user-facing tool; it's a scalable assistant for various business functions.

  * **How it Works:** The chatbot runs as a dedicated Lambda function (`finmate-chatbot`). A user's message triggers this function, which processes the query and generates a response. This design makes the chatbot a mini-API for conversational AI.
  * **Business Value:**
      * **24/7 Customer Support:** The bot can handle common questions, freeing up human agents for more complex issues.
      * **Lead Generation:** It can engage with potential customers, qualify leads, and direct them to relevant products.
      * **Personalized Insights:** When integrated with customer data, the bot can provide tailored investment tips or product recommendations.
----

#### The Market Data Lambda Functions

Multiple Lambda functions are used to act as a data layer, showcasing a **microservices** approach.

  * `finmate-jse-index`: This function is crucial for fetching data not available from other APIs (e.g., the JSE All Share Index). This demonstrates how Lambda can create custom endpoints for specialized data.
  * Other functions (e.g., `finmate-us-stocks`, `finmate-exchange-rates`): These functions act as dedicated, single-purpose APIs, each responsible for a specific data set.
  * **Business Value of this Architecture:**
      * **Cost-Effectiveness:** You only pay for the time the functions are active, which is much cheaper than running a continuous server.
      * **Infinite Scalability:** Lambda automatically scales to handle any amount of traffic, preventing the application from crashing during peak usage.
      * **Simplified Maintenance:** Each function is an independent service. If you need to update one function (e.g., how JSE data is fetched), you only need to change that specific function without affecting the rest of the application.

<img width="1893" height="556" alt="image" src="https://github.com/user-attachments/assets/16ce4e64-f2d4-4c29-b280-d544bc032924" />

-----

### API Configuration & Setup

To ensure the application works correctly, you must update the `index.html` file with your specific API credentials.

  * **API Keys:** Replace the placeholder text for `FINNHUB_API_KEY` and `JSE_LAMBDA_URL` with your actual key and function URL.

<!-- end list -->

```javascript
// Replace with your Finnhub API key and JSE Lambda Function URL
const FINNHUB_API_KEY = "YOUR_FINNHUB_API_KEY_HERE";
const JSE_LAMBDA_URL = "YOUR_JSE_LAMBDA_FUNCTION_URL_HERE";
```
* **Data Refresh Rate:** 
    * The application is configured to update all market data and exchange rates every 5 minutes to help you stay within API rate limits.
