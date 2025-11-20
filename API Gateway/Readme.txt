# 🌐 Amazon API Gateway — Serverless Event-Driven File Tracker

## 📘 Overview

In this project, **Amazon API Gateway** acts as the **public-facing REST API layer** that connects clients (like browsers or applications) to AWS Lambda functions.
It enables users to securely access data stored in **Amazon DynamoDB** via the `getFilesFunction` Lambda — making the system fully serverless and event-driven.

---

## ⚙️ Configuration Details

| Parameter            | Description                                                          |
| -------------------- | -------------------------------------------------------------------- |
| **API Type**         | HTTP API (Recommended for serverless workloads)                      |
| **Integration Type** | Lambda Proxy Integration                                             |
| **Lambda Function**  | `getFilesFunction`                                                   |
| **HTTP Method**      | `GET`                                                                |
| **Route Path**       | `/files`                                                             |
| **Deployment Stage** | `$default`                                                           |
| **Authentication**   | Open (Public access for testing; can be secured with IAM or Cognito) |

---

## 🔁 Role in the Architecture

**API Gateway** provides a **REST interface** for accessing filenames stored in DynamoDB.
When a request is made to `/files`, API Gateway automatically invokes the `getFilesFunction` Lambda, which retrieves data from DynamoDB and returns it as a JSON response.

📈 **Workflow:**

```
Client Request (GET /files)
          ↓
     API Gateway
          ↓
 getFilesFunction (Lambda)
          ↓
     DynamoDB (files)
          ↓
    JSON Response to Client
```

---

## 🧩 API Configuration Steps

### Step 1️⃣ — Create API

1. Navigate to **API Gateway → Create API**
2. Select **HTTP API (Recommended)**
3. Click **Build**

### Step 2️⃣ — Integration

* Choose **Lambda** as the integration type.
* Select the function **`getFilesFunction`**.
* Continue to **Next**.

### Step 3️⃣ — Create Route

| Field                  | Value              |
| ---------------------- | ------------------ |
| **Method**             | `GET`              |
| **Resource Path**      | `/files`           |
| **Integration Target** | `getFilesFunction` |

Click **Create route**.

### Step 4️⃣ — Deployment

* Default stage: `$default`
* API Gateway automatically deploys updates to this stage.

---

## 🌍 Invoke URL

After deployment, note the **Invoke URL** from the API overview page.

Example:

```
https://abc123xyz.execute-api.ap-south-1.amazonaws.com/files
```

📡 **Public Endpoint:**
When accessed in a browser, this URL triggers the Lambda → DynamoDB workflow.

---

## 🧾 Example API Response

When you visit the `/files` endpoint, you’ll see:

```json
[
  {"filename": "hello.txt"},
  {"filename": "report.pdf"}
]
```

---

## 🔐 Permissions

For API Gateway to invoke Lambda, Lambda’s resource policy must allow it.

**Example Lambda Permission (auto-added by AWS):**

```json
{
  "Effect": "Allow",
  "Principal": {
    "Service": "apigateway.amazonaws.com"
  },
  "Action": "lambda:InvokeFunction",
  "Resource": "arn:aws:lambda:ap-south-1:123456789012:function:getFilesFunction"
}
```

**Lambda IAM Role Requires:**

* `AmazonDynamoDBReadOnlyAccess`
* `AWSLambdaBasicExecutionRole`

---

## 🧠 Monitoring

* Use **CloudWatch Logs → /aws/lambda/getFilesFunction**
* Every API call generates a log event in Lambda’s log group.
* Monitor metrics like:

  * Request count
  * Latency
  * 4xx/5xx errors

---


## 🧾 Summary

| Resource               | Value                                 |
| ---------------------- | ------------------------------------- |
| **API Type**           | HTTP API                              |
| **Route**              | `/files`                              |
| **Method**             | GET                                   |
| **Lambda Integration** | `getFilesFunction`                    |
| **Database**           | DynamoDB (`files`)                    |
| **Purpose**            | Publicly fetch uploaded file metadata |

---

## 🧑‍💻 Author

**Akash V** — Cloud & DevOps Engineer ☁️
Focus: AWS | Serverless | Terraform | CI/CD | Docker | Kubernetes
🌍 [LinkedIn](https://linkedin.com/in/akashvetriselvan/) | [GitHub](https://github.com/akashvetriselvan)
