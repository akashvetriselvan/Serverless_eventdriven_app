# 📊 Amazon CloudWatch — Serverless Event-Driven File Tracker

## Overview

**Amazon CloudWatch** monitors and logs all activity in this project. It automatically captures execution details from AWS Lambda and API Gateway, helping to debug, measure performance, and track errors in the serverless workflow.

---

## Configuration

* **Region:** `ap-south-1`
* **Log Groups:**

  * `/aws/lambda/processUploadedFile`
  * `/aws/lambda/getFilesFunction`
* **IAM Role Required:** `AWSLambdaBasicExecutionRole`

---

## Purpose

* Stores all Lambda logs and API Gateway events
* Provides metrics like Invocations, Duration, and Errors
* Helps identify failed uploads or DynamoDB write issues

---

## How to View Logs

1. Go to **CloudWatch → Logs → Log groups**
2. Select your Lambda’s log group
3. Open the latest **log stream** to view execution results

---

## Summary

CloudWatch enables full visibility into this AWS serverless pipeline, ensuring each component—S3, Lambda, DynamoDB, and API Gateway—runs reliably and efficiently.
