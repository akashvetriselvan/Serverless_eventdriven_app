# 🌐 Serverless Event-Driven File Tracker (AWS Free Tier Project)

## 📘 Overview

This project demonstrates a **serverless event-driven architecture** on AWS that automatically processes file uploads, stores metadata, and provides an API for retrieving that data.
It’s built entirely on **AWS Free Tier** using **Amazon S3**, **Lambda**, **DynamoDB**, **API Gateway**, **CloudWatch**, and **IAM** — with Python 3.12 as the runtime.

---

## ⚙️ Architecture Diagram

<img width="1536" height="1024" alt="ChatGPT Image Nov 20, 2025, 01_32_11 PM" src="https://github.com/user-attachments/assets/bdd5a129-cd46-430f-b32b-243d7c059f2e" />


---

## 🧩 AWS Services Breakdown

### 🗂️ 1️⃣ Amazon S3 (Bucket: `serverless_event4`)

**Purpose:** Stores uploaded files and triggers Lambda events.

* **Bucket name:** `serverless_event4`
* **Event trigger:** *All object create events*
* **Trigger target:** `processUploadedFile` Lambda
* **Permissions:**

  * Lambda needs read access to S3 events (`s3:GetObject`)
  * S3 needs permission to invoke Lambda

📁 **What Happens:**
When you upload a file (like `hello.txt`), S3 automatically sends an event to Lambda.

---

### ⚡ 2️⃣ AWS Lambda

**Purpose:** Runs your automation code without managing servers.

| Function              | Trigger     | Task                                               | Output                        |
| --------------------- | ----------- | -------------------------------------------------- | ----------------------------- |
| `processUploadedFile` | S3 Upload   | Extracts filename and saves it in DynamoDB         | `{ "status": "saved" }`       |
| `getFilesFunction`    | API Gateway | Reads all filenames from DynamoDB and returns JSON | `[{"filename": "hello.txt"}]` |

#### 📄 processUploadedFile.py

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

#### 📄 getFilesFunction.py

```python
import boto3, json

def lambda_handler(event, context):
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('files')
    response = table.scan()

    return {
        "statusCode": 200,
        "headers": {"Content-Type": "application/json"},
        "body": json.dumps(response['Items'])
    }
```

✅ **Runtime:** Python 3.12
✅ **Architecture:** x86_64
✅ **Monitoring:** CloudWatch Logs
✅ **Permissions:**

* `AmazonDynamoDBFullAccess` (for write Lambda)
* `AmazonDynamoDBReadOnlyAccess` (for read Lambda)
* `AWSLambdaBasicExecutionRole`

---

### 🗃️ 3️⃣ Amazon DynamoDB (Table: `files`)

**Purpose:** Stores file metadata (filename).

* **Table name:** `files`
* **Partition key:** `filename` (String)
* **Access type:**

  * Write access: `processUploadedFile`
  * Read access: `getFilesFunction`

🧩 **Example Data:**

| filename   |
| ---------- |
| hello.txt  |
| report.pdf |

---

### 🌐 4️⃣ API Gateway

**Purpose:** Provides a public REST API to retrieve stored filenames.

| Route    | Method | Integration        | Lambda             |
| -------- | ------ | ------------------ | ------------------ |
| `/files` | GET    | Lambda Integration | `getFilesFunction` |

* **Invoke URL Example:**

  ```
  https://your-api-id.execute-api.ap-south-1.amazonaws.com/files
  ```

📡 **When you access this URL:**

1. API Gateway triggers `getFilesFunction`
2. Lambda reads from DynamoDB
3. API Gateway returns the JSON response

🧾 **Expected Output:**

```json
[
  {"filename": "hello.txt"},
  {"filename": "report.pdf"}
]
```

---

### 🧩 5️⃣ CloudWatch

**Purpose:** Logs every Lambda execution and helps in debugging.

* Log group names:

  ```
  /aws/lambda/processUploadedFile
  /aws/lambda/getFilesFunction
  ```
* You can view:

  * Function start and end times
  * Errors or missing permissions
  * Data processed by Lambda

---

### 🔐 6️⃣ IAM Roles

**Purpose:** Manages secure access between AWS services.

| Role                | Attached Policies                     |
| ------------------- | ------------------------------------- |
| LambdaExecutionRole | AWSLambdaBasicExecutionRole           |
| WriteLambdaRole     | AmazonDynamoDBFullAccess              |
| ReadLambdaRole      | AmazonDynamoDBReadOnlyAccess          |
| S3InvokePermission  | Grants S3 permission to invoke Lambda |

---

## 🧪 Testing Steps

1. **Upload File to S3:**
   Go to the S3 bucket `serverless_event4` → Upload `test.txt`.
2. **Lambda Trigger:**
   S3 event triggers `processUploadedFile` automatically.
3. **Check DynamoDB:**
   Go to `files` table → “Explore table items” → confirm filename is saved.
4. **Access API:**
   Open your API URL in a browser:

   ```
   https://your-api-id.execute-api.ap-south-1.amazonaws.com/files
   ```
5. **View Output:**
   JSON list of filenames appears — success! 🎉

---

## 📸 Screenshots (for GitHub)

* S3 bucket event trigger setup
* DynamoDB table items
* CloudWatch logs showing Lambda execution
* API output in browser

---

## 💡 Real-World Use Cases

* Automated document or image tracking systems
* File metadata extraction for analytics
* Invoice or report management workflows
* Serverless file ingestion pipelines
* Scalable upload APIs for web/mobile apps

---

## 🧠 Learning Outcomes

You’ll gain hands-on experience in:

* Building **event-driven serverless workflows**
* Creating **Lambda → DynamoDB → API Gateway** integrations
* Managing IAM permissions
* Monitoring and debugging using CloudWatch
* Deploying fully automated backend systems

---

## 🧑‍💻 Author

**Akash V** — Cloud & DevOps Enthusiast ☁️
💼 Focus: AWS | Serverless | Terraform | CI/CD | Docker | Kubernetes
🌍 [LinkedIn](https://linkedin.com/in/akashvetriselvan/) | [GitHub](https://github.com/akashvetriselvan)

---


Would you like me to create a **small "About" section + topic tags (keywords)** for your GitHub repo to make it look even more polished when published?
