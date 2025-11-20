# 🗃️ Amazon DynamoDB — Serverless Event-Driven File Tracker

## 📘 Overview

In this project, **Amazon DynamoDB** serves as a **serverless, NoSQL database** used to store file metadata processed by AWS Lambda.
Each time a new file is uploaded to the S3 bucket `serverless_event4`, a Lambda function (`processUploadedFile`) automatically writes the filename into the DynamoDB table `files`.
This design provides fast, reliable, and fully managed data storage without needing a traditional database server.

---

## ⚙️ Configuration Details

| Parameter                       | Description                                      |
| ------------------------------- | ------------------------------------------------ |
| **Table Name**                  | `files`                                          |
| **Region**                      | `ap-south-1`                                     |
| **Primary Key (Partition Key)** | `filename` *(String)*                            |
| **Billing Mode**                | On-demand (Pay per request - Free Tier eligible) |
| **Encryption**                  | AWS-managed key (default)                        |
| **Stream Settings**             | Disabled (not required for this project)         |

---

## 🔁 Role in the Architecture

DynamoDB acts as the **persistent storage layer** in this event-driven system.

📈 **Workflow:**

```
S3 Upload → Lambda (processUploadedFile) → DynamoDB (files) → Lambda (getFilesFunction) → API Gateway
```

* **processUploadedFile Lambda:** Writes new filename entries into the table
* **getFilesFunction Lambda:** Reads all stored filenames and exposes them via a REST API
* **API Gateway:** Acts as the public access layer for retrieving data

This makes DynamoDB the **central data source** for tracking uploaded files.

---

## 🧩 Table Schema

| Attribute  | Type   | Description                              |
| ---------- | ------ | ---------------------------------------- |
| `filename` | String | Unique identifier for each uploaded file |

🧱 Example Data:

| filename      |
| ------------- |
| `hello.txt`   |
| `report.pdf`  |
| `invoice.csv` |

---

## 🔐 IAM Permissions

### Required for **processUploadedFile** (Write Access)

```json
{
  "Effect": "Allow",
  "Action": [
    "dynamodb:PutItem"
  ],
  "Resource": "arn:aws:dynamodb:ap-south-1:*:table/files"
}
```

### Required for **getFilesFunction** (Read Access)

```json
{
  "Effect": "Allow",
  "Action": [
    "dynamodb:Scan",
    "dynamodb:GetItem"
  ],
  "Resource": "arn:aws:dynamodb:ap-south-1:*:table/files"
}
```

These permissions are attached via managed policies:

* `AmazonDynamoDBFullAccess` (for write Lambda)
* `AmazonDynamoDBReadOnlyAccess` (for read Lambda)

---

## 🧠 Common Operations

| Operation   | Description                                | Lambda Function          |
| ----------- | ------------------------------------------ | ------------------------ |
| **PutItem** | Adds a new file record                     | `processUploadedFile`    |
| **Scan**    | Retrieves all items from the table         | `getFilesFunction`       |

---

## 🧾 Example Lambda Interaction

### ➤ Writing Data (from `processUploadedFile`)

```python
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('files')
table.put_item(Item={"filename": filename})
```

### ➤ Reading Data (from `getFilesFunction`)

```python
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('files')
response = table.scan()
return response['Items']
```

---

## 🧩 Example API Response

Accessing `/files` via API Gateway returns:

```json
[
  {"filename": "hello.txt"},
  {"filename": "report.pdf"}
]
```

---

## 📊 Monitoring and Metrics

You can monitor DynamoDB performance and usage in **AWS CloudWatch → Metrics → DynamoDB**.
Metrics include:

* Read/Write Capacity Units
* Throttled Requests
* Latency
* Item Count


---

## 🧾 Summary

| Resource         | Value                                       |
| ---------------- | ------------------------------------------- |
| **Table Name**   | `files`                                     |
| **Primary Key**  | `filename`                                  |
| **Write Source** | `processUploadedFile` Lambda                |
| **Read Source**  | `getFilesFunction` Lambda                   |
| **Purpose**      | Store filenames for all uploaded S3 objects |

---

## 🧑‍💻 Author

**Akash V** — Cloud & DevOps Engineer ☁️
Specializing in Serverless Architectures, AWS Automation, and CI/CD Pipelines.
🌍 [LinkedIn](https://linkedin.com/in/akashvetriselvan/) | [GitHub](https://github.com/akashvetriselvan)

---
