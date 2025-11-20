# 🗂️ Amazon S3 — Serverless Event-Driven File Tracker

## 📘 Overview

In this project, **Amazon S3** acts as the **entry point** of a fully serverless, event-driven workflow on AWS.
The S3 bucket — `serverless_event4` — securely stores uploaded files and automatically triggers downstream automation using AWS Lambda.
This setup demonstrates real-world event-driven design used in scalable cloud applications.

---

## ⚙️ Configuration Details

| Parameter               | Description                             |
| ----------------------- | --------------------------------------- |
| **Bucket Name**         | `serverless_event4`                     |
| **Region**              | `ap-south-1 (Asia Pacific - Mumbai)`    |
| **Event Trigger**       | All object create events                |
| **Trigger Destination** | Lambda function — `processUploadedFile` |
| **Public Access**       | Blocked (Recommended for security)      |
| **Storage Class**       | Standard                                |
| **Versioning**          | Optional (Disabled in this project)     |

---

## 🔁 Role in the Architecture

Amazon S3 is responsible for:

* Accepting file uploads from users or applications
* Generating event notifications when new files are added
* Automatically invoking the **Lambda function** `processUploadedFile`
* Passing metadata (like filename and object key) to Lambda for further processing

This enables a **serverless, event-driven workflow** without the need for any manual operations or servers.

---

## 🧩 Event Notification Setup

### Steps (AWS Console)

1. Navigate to **Amazon S3 → Buckets → serverless_event4**
2. Go to **Properties → Event notifications**
3. Click **Create event notification**
4. Configure:

   | Field               | Value                    |
   | ------------------- | ------------------------ |
   | **Name**            | `lambda-trigger`         |
   | **Event Type**      | All object create events |
   | **Destination**     | Lambda function          |
   | **Lambda Function** | `processUploadedFile`    |
5. Save changes ✅

This configuration ensures every file upload triggers Lambda instantly.

---

## 🧾 Example S3 Event (JSON Payload)

When a file is uploaded, S3 sends the following event structure to Lambda:

```json
{
  "Records": [
    {
      "eventName": "ObjectCreated:Put",
      "s3": {
        "bucket": { "name": "serverless_event4" },
        "object": { "key": "hello.txt", "size": 2345 }
      }
    }
  ]
}
```

Lambda extracts the `"object.key"` (filename) and stores it in DynamoDB via boto3 SDK.

---

## 🔐 Security & IAM Permissions

S3 requires permission to **invoke Lambda**, and the Lambda execution role must have permission to **read objects** from the bucket.

**Required Policies:**

* **For Lambda Role:**

  ```json
  {
    "Effect": "Allow",
    "Action": ["s3:GetObject"],
    "Resource": "arn:aws:s3:::serverless_event4/*"
  }
  ```
* **For S3 Bucket:**
  Add permission to invoke the `processUploadedFile` Lambda function.

**Best Practices:**

* Keep public access **blocked** for security.
* Use AWS-managed IAM roles and least privilege access.

---

## 🧠 Common Operations

| Task             | Description                                                                 |
| ---------------- | --------------------------------------------------------------------------- |
| **Upload Files** | Use the AWS Console or AWS CLI to upload files into `serverless_event4`.    |
| **View Files**   | Navigate to **Objects** tab to view uploads.                                |
| **Event Test**   | Upload a sample file (`test.txt`) and confirm Lambda trigger in CloudWatch. |
| **Cleanup**      | Delete test files to maintain Free Tier usage.                              |

---

## 🧾 Summary

| Resource             | Value                                |
| -------------------- | ------------------------------------ |
| **Bucket Name**      | `serverless_event4`                  |
| **Trigger Function** | `processUploadedFile`                |
| **Trigger Type**     | All object create events             |
| **Purpose**          | File storage and Lambda event source |
| **Integration**      | S3 → Lambda → DynamoDB               |

---

## 🧑‍💻 Author

**Akash V** — Cloud & DevOps Engineer ☁️
Expertise: AWS | Serverless | Terraform | Docker | Kubernetes | CI/CD
🌍 [LinkedIn](https://linkedin.com/in/akashvetriselvan/) | [GitHub](https://github.com/akashvetriselvan)

---
