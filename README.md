# 🌐 Serverless Event-Driven File Tracker (AWS Free Tier Project)

## 📘 Overview

This project demonstrates a **serverless event-driven architecture** on AWS that automatically processes file uploads, stores metadata, and exposes data through a REST API endpoint — all using **AWS Free Tier services**.
It’s a practical, real-world cloud project that showcases automation, scalability, and integration across AWS services without managing any servers.

---

## ⚙️ Architecture Overview

![Architecture Diagram]([/mnt/data/A_diagram_in_the_image_illustrates_a_serverless_ar.png](https://drive.google.com/file/d/1KOoYxp_OK-RiI3xwF9dMmkkkGaRbJ8Jo/view?usp=sharing))

### AWS Services Used

| Service                | Purpose                                             |
| ---------------------- | --------------------------------------------------- |
| **Amazon S3**          | Stores uploaded files and triggers Lambda functions |
| **AWS Lambda**         | Processes uploaded files and retrieves metadata     |
| **Amazon DynamoDB**    | Stores filenames in a NoSQL table                   |
| **Amazon API Gateway** | Provides public REST API endpoint `/files`          |
| **AWS CloudWatch**     | Logs Lambda events for debugging and monitoring     |
| **AWS IAM**            | Controls secure access between AWS services         |

---

## 🔁 Workflow

1. **File Upload** — A user uploads a file to the **S3 bucket** `serverless_event4`.
2. **S3 Trigger** — The upload automatically triggers the Lambda function `processUploadedFile`.
3. **Lambda Processing** — The Lambda extracts the filename and stores it in the **DynamoDB table** `files`.
4. **Data Retrieval** — Another Lambda function `getFilesFunction` reads all filenames from DynamoDB.
5. **API Access** — Users fetch data through a public API endpoint via API Gateway.

📈 **Flow Diagram:**

```
S3 (serverless_event4) → processUploadedFile Lambda → DynamoDB (files)
                                            ↑
                                         getFilesFunction Lambda
                                            ↑
                                       API Gateway (/files)
                                            ↑
                                          Browser
```

---

## 🧩 Lambda Functions

### 1️⃣ processUploadedFile.py

Triggered by S3 → Extracts filename → Stores it in DynamoDB.

```python
import boto3

def lambda_handler(event, context):
    record = event['Records'][0]
    filename = record['s3']['object']['key']

    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('files')

    table.put_item(Item={"filename": filename})

    print(f"Saved to DynamoDB: {filename}")

    return {"status": "saved", "filename": filename}
```

**Trigger:**

* S3 bucket: `serverless_event4`
* Event type: *All object create events*

**Permissions:**

* `AmazonDynamoDBFullAccess`
* `AWSLambdaBasicExecutionRole`

---

### 2️⃣ getFilesFunction.py

Triggered by API Gateway → Retrieves all filenames from DynamoDB → Returns as JSON.

```python
import boto3
import json

def lambda_handler(event, context):
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('files')

    response = table.scan()
    items = response['Items']

    return {
        "statusCode": 200,
        "headers": {"Content-Type": "application/json"},
        "body": json.dumps(items)
    }
```

**Trigger:**

* API Gateway HTTP API → `/files`
  **Permissions:**
* `AmazonDynamoDBReadOnlyAccess`
* `AWSLambdaBasicExecutionRole`

---

## 🧪 Testing Steps

1. Upload a file to S3 → `serverless_event4`.
2. Wait for the S3 → Lambda trigger to execute.
3. Check DynamoDB → `files` table → **Explore table items**.
4. Access the API Gateway endpoint (example):

   ```
   https://your-api-id.execute-api.ap-south-1.amazonaws.com/files
   ```
5. Expected Output:

   ```json
   [
     {"filename": "hello.txt"},
     {"filename": "report.pdf"}
   ]
   ```

---

## 📸 Screenshots (Add these to your repo)

| Feature        | Example Screenshot               |
| -------------- | -------------------------------- |
| S3 Upload      | `screenshots/s3-upload.png`      |
| DynamoDB Table | `screenshots/dynamodb-table.png` |
| Lambda Logs    | `screenshots/lambda-logs.png`    |
| API Response   | `screenshots/api-gateway.png`    |

---

## 💡 Real-World Applications

* Automated document or image upload tracking
* File processing & metadata extraction
* Invoice/report management workflows
* Data ingestion for analytics
* Scalable backend for upload-based web apps

---

## 🧠 Learning Outcomes

By completing this project, you learned:

* Event-driven automation with **S3 triggers**
* **Lambda → DynamoDB → API Gateway** integration
* Using **boto3 SDK** in Lambda for AWS interactions
* **IAM roles** and permissions management
* Testing and debugging via **CloudWatch Logs**

---

## 🧑‍💻 Author

**Akash V** — Cloud & DevOps Enthusiast ☁️
💼 Focus: AWS | Serverless | Terraform | CI/CD | Docker | Kubernetes
🌍 [LinkedIn]([https://linkedin.com](https://www.linkedin.com/in/akashvetriselvan/)) | [GitHub]([https://github.com](https://github.com/akashvetriselvan))

---
