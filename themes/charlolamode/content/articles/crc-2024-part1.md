---
title: AWS Cloud Resume Challenge Blog Post Part 1
type: page
description: Building a Cloud-Native Website with AWS From Static Hosting to Dynamic Content
topic: career
---

"Building a Cloud-Native Website with AWS: From Static Hosting to Dynamic Content"

1. Introduction
2. Setting Up a Static Website with S3
3. Securing the Website with CloudFront
4. Configuring DNS with Route 53
5. Implementing a Visitor Counter
   - DynamoDB for Data Storage
   - Lambda for Backend Logic
   - API Gateway for HTTP Endpoints
6. Frontend JavaScript Integration
7. Testing the JavaScript Code
8. Conclusion

---

# Building a Cloud-Native Website with AWS: From Static Hosting to Dynamic Content

I heard about the Cloud Resume Challenge while surfing reddit a few years ago. As a tech enthusiast but someone who is not working in AWS on a daily basis, the challenge intrigued me. 

The Cloud Resume Challenge, created by Forrest Brazeal, is a multi-step project that involves building a full-stack application using various AWS services. It's designed to showcase cloud skills through practical implementation, making it an excellent exercise even for those of us already in the field.

In this blog post, I'll share my experience completing the Cloud Resume Challenge, detailing the AWS services I used, the architecture I implemented, and the insights I gained. Whether you're a cloud professional looking to sharpen your skills or someone curious about real-world AWS applications, I hope you'll find value in my journey through this project.

## Table of Contents

1. [Introduction](#introduction)
2. [Setting Up a Static Website with S3](#setting-up-a-static-website-with-s3)
3. [Securing the Website with CloudFront](#securing-the-website-with-cloudfront)
4. [Configuring DNS with Route 53](#configuring-dns-with-route-53)
5. [Implementing a Visitor Counter](#implementing-a-visitor-counter)
6. [Frontend JavaScript Integration](#frontend-javascript-integration)
7. [Testing the JavaScript Code](#testing-the-javascript-code)
8. [Conclusion](#conclusion)

## Introduction

Cloud-native development leverages the power of cloud computing to build scalable and resilient applications. In this comprehensive guide, we'll walk through the process of creating a cloud-native website using various AWS services. We'll start with hosting a static website and progressively add dynamic features, including a visitor counter. This article is perfect for beginners who want to understand how different AWS services work together to create a fully functional, scalable website.

## Setting Up a Static Website with S3

Amazon S3 (Simple Storage Service) is an object storage service that we can use to host static websites. Here's how to set it up:

1. Create an S3 bucket:
   - Go to the AWS Management Console and navigate to S3.
   - Click "Create bucket" and choose a globally unique name.
   - In the bucket settings, enable "Static website hosting".
   - Set the index document to "index.html".

2. Upload your website files:
   - Upload your HTML, CSS, and JavaScript files to the bucket.
   - Ensure the files have public read permissions.

3. Configure bucket policy:
   - In the bucket permissions, add a bucket policy to allow public read access:

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

   Replace "your-bucket-name" with your actual bucket name.

## Securing the Website with CloudFront

CloudFront is AWS's Content Delivery Network (CDN) service. We'll use it to serve our website over HTTPS and improve performance:

1. Create a CloudFront distribution:
   - In the AWS Console, go to CloudFront and click "Create Distribution".
   - Set the origin domain to your S3 bucket's website endpoint.
   - Enable "Redirect HTTP to HTTPS".

2. Configure SSL/TLS certificate:
   - Use AWS Certificate Manager (ACM) to create a free SSL/TLS certificate for your domain.
   - In your CloudFront distribution settings, select this certificate.

3. Set up Origin Access Identity (OAI):
   - Create an OAI and associate it with your distribution.
   - Update your S3 bucket policy to only allow access from this OAI.

## Configuring DNS with Route 53

Route 53 is AWS's Domain Name System (DNS) web service. We'll use it to map our domain name to our CloudFront distribution:

1. Create a hosted zone:
   - In Route 53, create a hosted zone for your domain.

2. Update name servers:
   - If your domain is registered elsewhere, update the name servers at your registrar to use Route 53's name servers.

3. Create record sets:
   - Create an A record for your domain, aliasing it to your CloudFront distribution.
   - If needed, create a CNAME record for www subdomain.

## Implementing a Visitor Counter

For our visitor counter, we'll use a serverless architecture with DynamoDB, Lambda, and API Gateway.

### DynamoDB for Data Storage

DynamoDB is a NoSQL database service. We'll use it to store our visitor count:

1. Create a DynamoDB table:
   - Name: "VisitorCount"
   - Partition key: "id" (String)
   - Add an item with id "visitors" and an attribute "count" set to 0.

### Lambda for Backend Logic

Lambda allows us to run code without provisioning servers. Our Lambda function will update the visitor count:

1. Create a Lambda function:
   - Runtime: Node.js 20.x
   - Code:

   ```javascript
   const { DynamoDBClient, UpdateItemCommand } = require('@aws-sdk/client-dynamodb');
   
   const client = new DynamoDBClient();
   
   exports.handler = async (event) => {
       const params = {
           TableName: process.env.TABLE_NAME,
           Key: { id: { S: 'visitors' } },
           UpdateExpression: 'ADD visitorCount :inc',
           ExpressionAttributeValues: { ':inc': { N: '1' } },
           ReturnValues: 'UPDATED_NEW'
       };
   
       try {
           const command = new UpdateItemCommand(params);
           const data = await client.send(command);
           
           const visitorCount = data.Attributes.visitorCount.N;
   
           return {
               statusCode: 200,
               headers: {
                   "Access-Control-Allow-Origin": "https://yourdomain.com",
                   "Access-Control-Allow-Headers": "Content-Type",
                   "Access-Control-Allow-Methods": "OPTIONS,POST"
               },
               body: JSON.stringify({ visitorCount: parseInt(visitorCount) })
           };
       } catch (error) {
           console.error('Error:', error);
           return {
               statusCode: 500,
               headers: {
                   "Access-Control-Allow-Origin": "https://yourdomain.com",
                   "Access-Control-Allow-Headers": "Content-Type",
                   "Access-Control-Allow-Methods": "OPTIONS,POST"
               },
               body: JSON.stringify({ error: 'Failed to update visitor count' })
           };
       }
   };
   ```

2. Set up environment variables:
   - Add TABLE_NAME environment variable with your DynamoDB table name.

3. Configure permissions:
   - Give the Lambda function permission to read/write to your DynamoDB table.

### API Gateway for HTTP Endpoints

API Gateway allows us to create, publish, and manage APIs. We'll use it to expose our Lambda function:

1. Create a new API:
   - Choose HTTP API type.

2. Create a resource and method:
   - Create a resource named "visitorcount".
   - Add a POST method to this resource, integrating it with your Lambda function.

3. Enable CORS:
   - In the API settings, enable CORS for your domain.

4. Deploy the API:
   - Create a new stage (e.g., "prod") and deploy your API.

## Frontend JavaScript Integration

Now, let's integrate our backend with the frontend:

1. Add HTML for the visitor counter:

   ```html
   <div id="visitor-count">Visitors: <span id="count">0</span></div>
   ```

2. Add JavaScript to call our API:

   ```javascript
   function updateVisitorCount() {
       fetch('https://your-api-gateway-url/visitorcount', {
           method: 'POST',
           headers: {
               'Content-Type': 'application/json'
           },
           mode: 'cors'
       })
       .then(response => response.json())
       .then(data => {
           document.getElementById('count').textContent = data.visitorCount;
       })
       .catch(error => console.error('Error:', error));
   }

   document.addEventListener('DOMContentLoaded', updateVisitorCount);
   ```

   Replace 'https://your-api-gateway-url' with your actual API Gateway URL.

## Testing the JavaScript Code

To ensure our JavaScript works correctly, we'll add some tests:

1. Install Jest for testing:
   ```
   npm install --save-dev jest
   ```

2. Create a test file (e.g., `visitorCounter.test.js`):

   ```javascript
   // Mock fetch function
   global.fetch = jest.fn(() =>
     Promise.resolve({
       json: () => Promise.resolve({ visitorCount: 5 }),
     })
   );

   // Import the function to test
   const { updateVisitorCount } = require('./visitorCounter');

   test('updates visitor count correctly', async () => {
     // Set up our document body
     document.body.innerHTML = '<div id="count">0</div>';

     // Call the function
     await updateVisitorCount();

     // Check that the count was updated
     expect(document.getElementById('count').textContent).toBe('5');
   });
   ```

3. Run the tests:
   ```
   npx jest
   ```

## Conclusion

In this guide, we've covered how to build a cloud-native website using various AWS services. We've implemented a static website with S3, secured it with CloudFront, set up DNS with Route 53, and created a serverless backend with DynamoDB, Lambda, and API Gateway.

This architecture provides a scalable, secure, and cost-effective solution for hosting websites and web applications. As you become more comfortable with these services, you can expand on this foundation to build even more complex and feature-rich applications.

In the next article, we'll explore how to automate the deployment of this website using CI/CD practices with GitHub Actions.
