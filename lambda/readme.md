⚡ AWS Lambda Functions — Serverless Event-Driven App

This folder contains two AWS Lambda functions used in the **Serverless Event-Driven File Tracker Project**.
Both are written in **Python 3.12**, using the **boto3 AWS SDK**, and play key roles in automating file handling and data retrieval.

---

📁 Lambda Functions Overview

| Function Name         | Purpose                                              | Trigger     | Connected Service |
| --------------------- | ---------------------------------------------------- | ----------- | ----------------- |
| `processUploadedFile` | Triggered by S3 upload, stores filename in DynamoDB  | S3 Event    | DynamoDB          |
| `getFilesFunction`    | Returns all stored filenames through an API endpoint | API Gateway | DynamoDB          |

---

🧩 1️⃣ processUploadedFile.py

**Purpose:**
Triggered automatically when a new file is uploaded to the S3 bucket.
It captures the filename and stores it in the DynamoDB table named **`files`**.

🔹 Code:python

import boto3
def lambda_handler(event, context):
    # Get uploaded file info from S3 event
    record = event['Records'][0]
    filename = record['s3']['object']['key']

    # Connect to DynamoDB
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('files')

    # Insert file record
    table.put_item(Item={"filename": filename})

    print(f"Saved to DynamoDB: {filename}")

    return {
        "status": "saved",
        "filename": filename
    }
```

🔹 Trigger:

* **Amazon S3 → Event notification**
* Type: *All object create events*
* Destination: *Lambda function (processUploadedFile)*

🔹 Permissions Required:

* `AmazonDynamoDBFullAccess`
* `AWSLambdaBasicExecutionRole` (for CloudWatch logging)

---

🧩 2️⃣ getFilesFunction.py

**Purpose:**
Exposes an API endpoint (via API Gateway) that retrieves all stored file records from DynamoDB and returns them as JSON.

🔹 Code:python

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

🔹 Trigger:

* **API Gateway → HTTP API route**
* Method: `GET`
* Resource path: `/files`
* Integration: Lambda function (`getFilesFunction`)

🔹 Permissions Required:

* `AmazonDynamoDBReadOnlyAccess`
* `AWSLambdaBasicExecutionRole`

---

⚙️ Lambda Environment Details

| Setting          | Value                                                             |
| ---------------- | ----------------------------------------------------------------- |
| **Runtime**      | Python 3.12                                                       |
| **Architecture** | x86_64                                                            |
| **Timeout**      | Default (3 seconds is enough for this project)                    |
| **Handler**      | `lambda_function.lambda_handler`                                  |
| **Region**       | ap-south-1 (keep consistent across S3, DynamoDB, and API Gateway) |

---

🧠 How Both Lambdas Work Together

```
File Upload (S3)
       ↓ (Event Trigger)
processUploadedFile (Lambda)
       ↓
 DynamoDB (Stores filename)
       ↑
getFilesFunction (Lambda)
       ↑ (API Gateway /files)
       ↑
      Browser or App
```

✅ **Result:**
Every file you upload to S3 automatically gets recorded in DynamoDB, and you can view the list anytime using the public API endpoint.

---

🔍 Logs & Monitoring

Both Lambdas automatically send execution logs to **Amazon CloudWatch** under:

```
/aws/lambda/processUploadedFile
/aws/lambda/getFilesFunction
```

You can monitor:

* Function invocations
* Errors or permission issues
* DynamoDB write/read confirmations

---

🧾 Summary

| Function            | Trigger              | Action                            | Output                        |
| ------------------- | -------------------- | --------------------------------- | ----------------------------- |
| processUploadedFile | S3 upload            | Writes filename to DynamoDB       | Returns `{"status": "saved"}` |
| getFilesFunction    | API Gateway `/files` | Reads all filenames from DynamoDB | Returns JSON list             |

---

🧰 Libraries Used

* **boto3** – AWS SDK for Python
  Pre-installed in the Lambda runtime, used to interact with S3 and DynamoDB.

---

