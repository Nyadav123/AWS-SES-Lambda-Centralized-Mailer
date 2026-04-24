# 📧 AWS SES + Lambda Centralized Mailer

A scalable, serverless **centralized mailing system** built using AWS services like **Lambda, SES, S3, SQS, DynamoDB, CloudFront, and EventBridge**.
This solution supports **high-volume email processing**, **large file attachments**, **tracking (bounces/complaints)**, and **report generation**.

---

## 🚀 Architecture Overview

This system is designed to handle both **small and large email payloads** efficiently:

* **Small payloads** → Direct API call
* **Large payloads** → Pre-signed S3 upload flow

It ensures:

* High scalability via **SQS-based queueing**
* Reliable processing via **Lambda workers**
* Observability via **DynamoDB logging**
* Feedback tracking via **SES → SNS → SQS**

---

## 🧩 Key Components

### 1. API Layer

* **/send** → Handles small email requests directly
* **/generate-presign** → Generates pre-signed S3 URLs for large files

---

### 2. File Upload Handling (Large Files)

* Pre-signed **PUT URL** is generated
* Client uploads file directly to **S3 (multipart supported)**
* Object URL (via **CloudFront or S3**) is used later in email

---

### 3. Mailer Request Handler (Lambda)

* Prepares email payload
* Uploads metadata/content to S3 if required
* Pushes message to **SQS queue**

---

### 4. Queue-Based Processing (SQS)

* Decouples request ingestion from processing
* Ensures **retry and fault tolerance**

---

### 5. Mailer Send Worker (Lambda)

* Consumes messages from SQS
* Fetches file URLs (S3/CloudFront)
* Sends email via **AWS SES**
* Stores logs in **DynamoDB**

---

### 6. Logging & Storage (DynamoDB)

* Stores:

  * Email request metadata
  * Delivery status
  * Processing logs

---

### 7. SES Feedback Handling

* **SES → SNS → SQS → Lambda (Bounce Worker)**
* Handles:

  * Bounces
  * Complaints
* Updates logs in DynamoDB
* Can trigger alert/failure emails

---

### 8. Reporting System

* **EventBridge** triggers report generation
* **Report Aggregator (Lambda)**:

  * Reads logs from DynamoDB
  * Generates reports (Excel/CSV)
  * Uploads reports to S3
* Reports are shared with users

---

## 📊 Features

* ✅ Serverless & scalable architecture
* ✅ Supports large attachments via S3 pre-signed URLs
* ✅ Asynchronous processing with SQS
* ✅ Email delivery via AWS SES
* ✅ Bounce & complaint tracking
* ✅ Centralized logging (DynamoDB)
* ✅ Automated report generation
* ✅ CloudFront support for secure file access

---

## 🔄 Workflow Summary

1. Client calls API
2. (Optional) Uploads large file via pre-signed URL
3. Request is pushed to SQS
4. Lambda worker processes request
5. Email sent via SES
6. Logs stored in DynamoDB
7. Feedback (bounce/complaint) processed
8. Reports generated via EventBridge

---

## 🛠️ Tech Stack

* **AWS Lambda**
* **Amazon SES**
* **Amazon S3**
* **Amazon SQS**
* **Amazon SNS**
* **Amazon DynamoDB**
* **Amazon CloudFront**
* **Amazon EventBridge**
* **AWS Systems Manager (Parameter Store)**

---

## 📌 Use Cases

* Bulk email systems
* Notification services
* Report distribution platforms
* Systems requiring attachment-heavy emails
* Centralized communication platforms

---

## ⚙️ Future Enhancements

* Email templating system
* Retry & DLQ optimization
* Dashboard for monitoring (CloudWatch + Grafana)
* Multi-region failover
* Rate limiting & throttling

---

## 📎 Notes

* Ensure SES is out of **sandbox mode** for production
* Proper IAM roles are required for each service
* Use CloudFront for secure and faster file access
