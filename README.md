# Event-Announcement-System-using-AWS-Lambda-API-Gateway-CloudFront-and-SNS
---
A **serverless event announcement platform** built entirely on AWS services and managed through **Terraform**. This project allows users to subscribe via email to event notifications and enables administrators to create events that automatically notify all subscribers, ensuring seamless communication.

---
## Key Features

* **Serverless Architecture:** Entirely powered by AWS Lambda functions, ensuring zero server management.
* **CloudFront CDN Integration:** Fast global content delivery with HTTPS and smart caching.
* **Automated Infrastructure Deployment:** Deploy all services using a single `./deploy.sh` command.
* **Email Notifications:** Subscribers instantly get event updates via Amazon SNS.
* **Interactive Logging:** Easy debugging and monitoring of Lambda functions.
* **Infrastructure as Code:** Managed entirely with Terraform for reproducibility and maintainability.
* **Frontend Automation:** Scripts to update frontend files without redeploying the entire infrastructure.

---

## Architecture
![architecture](screenshots/lambda_logs.png)


All components are **fully automated with Terraform**, making deployments consistent and reproducible.

---
## Proof of Work

### Screenshots 
![Events Homepage](screenshots/homepage.png)
![Events Homepage](screenshots/homepage.png)
![Events Homepage](screenshots/homepage.png)
![Events Homepage](screenshots/homepage.png) 
![SNS Email Notification](screenshots/sns_email.png)
![SNS Email Notification](screenshots/sns_email.png)
![Create Event Form](screenshots/create_event.png)
![](screenshots/lambda_logs.png)
![](screenshots/lambda_logs.png)
![](screenshots/lambda_logs.png)
![](screenshots/lambda_logs.png)
![](screenshots/lambda_logs.png)
![](screenshots/lambda_logs.png)

## Components

* **S3 Bucket:** Hosts static website files and stores `events.json` for event data.
* **CloudFront:** CDN for low-latency global content delivery with HTTPS.
* **SNS (Simple Notification Service):** Manages email subscriptions and sends event notifications.
* **Lambda Functions:**

  * `SubscribeToSNSFunction`: Subscribes users to SNS topics.
  * `CreateEventFunction`: Adds new events to S3 and triggers notifications.
* **API Gateway:** Exposes REST API endpoints for frontend-backend communication.
* **Terraform:** Defines and manages all AWS resources.

---

## Features

* **Email Subscription:** Subscribers confirm their emails via SNS.
* **Event Creation & Notification:** Automatic emails to all subscribers.
* **Static Website Hosting:** S3 serves frontend for event display.
* **CloudFront CDN:** Fast and secure global delivery.
* **Automated Deployment & Scripts:** Full Terraform infrastructure deployment with `deploy.sh`.
* **Cache Management:** Automatic CloudFront cache invalidation for instant updates.

---

## Prerequisites

* AWS Account with admin permissions.
* AWS CLI configured with credentials.
* Terraform v1.0+ installed.
* Git (optional, for version control).

---

## Project Structure

```
.
├── terraform/
│   ├── main.tf                  # AWS resources definitions
│   ├── variables.tf             # Terraform variables
│   ├── outputs.tf               # Outputs like API URLs
│   └── terraform.tfvars.example # Example configuration
├── lambda/
│   ├── subscribe.py             # Handles SNS subscriptions
│   └── create_event.py          # Handles event creation
├── frontend/
│   ├── index.html               # Website frontend
│   ├── styles.css               # Frontend styling
│   └── events.json              # Event data
├── scripts/
│   └── update-frontend.sh       # Quick frontend updates
├── deploy.sh                     # Automated deployment script
└── README.md                     # Project documentation
```

---

## Deployment

### Quick Start (Automated)

```bash
# Clone repository
git clone <your-repo-url>
cd event-announcement-system

# Configure variables
cp terraform/terraform.tfvars.example terraform/terraform.tfvars
# Edit terraform.tfvars with your unique bucket name

# Deploy everything
./deploy.sh
```

The script handles:

* Terraform initialization and apply
* S3 bucket creation & frontend upload
* Lambda function deployment
* API Gateway setup
* CloudFront distribution & cache invalidation
* SNS topic creation
* Outputs all URLs and endpoints

**Total deployment time:** ~20-25 minutes

---

### Manual Deployment (Step by Step)

1. **Clone repository**

```bash
git clone <your-repo-url>
cd event-announcement-system
```

2. **Configure Terraform variables**

```bash
cp terraform/terraform.tfvars.example terraform/terraform.tfvars
# Edit terraform.tfvars:
aws_region = "ap-south-1"
bucket_name = "event-announcement-website-yourname"
cloudfront_price_class = "PriceClass_100"
```

3. **Initialize and deploy Terraform**

```bash
cd terraform
terraform init
terraform plan
terraform apply
# Confirm with 'yes'
```

4. **Update frontend with API Gateway URL**

Edit `frontend/index.html`:

```javascript
const API_BASE_URL = 'https://<api-gateway-id>.execute-api.ap-south-1.amazonaws.com/dev';
```

Upload changes:

```bash
aws s3 cp frontend/index.html s3://YOUR-BUCKET-NAME/index.html --content-type "text/html"
aws cloudfront create-invalidation --distribution-id YOUR-DISTRIBUTION-ID --paths "/*"
```

5. **Access website**

* CloudFront URL: `https://xxxxxxxx.cloudfront.net`
* Direct S3 URL: `http://YOUR-BUCKET-NAME.s3-website.ap-south-1.amazonaws.com`

---

## Testing

**Email Subscription:**

1. Click "Subscribe to Events" on the site.
2. Enter email and confirm via AWS SNS email link.

**Event Creation:**

1. Click "Create New Event".
2. Enter event details and submit.
3. Confirm emails are sent to subscribers.
4. Verify event appears on website.

**Manual Lambda Testing:**

```json
# Subscribe Lambda test event
{
  "body": { "email": "test@example.com" }
}

# Create Event Lambda test event
{
  "httpMethod": "POST",
  "body": "{\"title\":\"Tech Meetup\",\"date\":\"2025-12-01\",\"description\":\"Tech event!\"}"
}
```

---

## Monitoring & Logs

* **Interactive log viewer:** `./deploy.sh logs`
* **Manual CloudWatch logs:**

```bash
aws logs tail /aws/lambda/SubscribeToSNSFunction --follow
aws logs tail /aws/lambda/createEventFunction --follow
```

* **CloudFront Monitoring:** AWS Console → CloudFront → Distributions → Monitoring

---

## Troubleshooting

* **Website not loading:** Check CloudFront deployment or use S3 direct URL.
* **Subscription failing:** Verify email confirmation and API URLs.
* **Events not appearing:** Check Lambda logs, S3 permissions, and SNS topic.
* **Old content in CloudFront:** Run cache invalidation: `./deploy.sh update`

---

## Cost Considerations

AWS Free Tier eligible services:

* **S3:** 5GB storage free
* **CloudFront:** 1TB data transfer free
* **Lambda:** 1M requests/month free
* **SNS:** 1,000 email notifications free
* **API Gateway:** 1M API calls/month free

Estimated monthly cost after free tier: $1–10 depending on usage.

---

## Security Best Practices

* Use least-privilege IAM roles
* Enable S3 versioning
* Consider API Gateway authentication
* Enable CloudTrail logging
* Use AWS WAF for API protection
* Store sensitive data in AWS Secrets Manager

---

## Customization

* Change AWS Region in `terraform.tfvars`.
* Modify CloudFront price class.
* Update event data structure in Lambda and frontend.
* Customize email notifications in `create_event.py`.
* Style frontend via `styles.css`.
* Add a custom domain with ACM SSL certificate (optional).

---

## Helper Scripts

* **`deploy.sh`**: Full automation for deployment, updates, logs, and tests.
* **`scripts/update-frontend.sh`**: Quick frontend updates without full redeployment.

---

## Additional Resources

* [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/)
* [Amazon SNS Documentation](https://docs.aws.amazon.com/sns/)
* [Amazon CloudFront Documentation](https://docs.aws.amazon.com/cloudfront/)
* [API Gateway Documentation](https://docs.aws.amazon.com/apigateway/)
* [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

