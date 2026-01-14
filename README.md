# Serverless Document Processing Pipeline (AWS)

## Overview
This project implements a fully serverless, event-driven document processing system on AWS. When a PDF is uploaded to Amazon S3, the system automatically extracts text using Amazon Textract, stores metadata and results in DynamoDB, and exposes secure REST APIs through Amazon API Gateway.

The solution is scalable, cost-efficient, and production-ready using only managed AWS services.

---

## Architecture Diagram

```mermaid
flowchart LR
    User[User]
    S3[S3 Bucket<br/>uploads/]
    L1[Lambda<br/>document-ingest-handler]
    TX[Amazon Textract<br/>Async OCR]
    EB[EventBridge Scheduler]
    L2[Lambda<br/>textract-poller]
    DDB[DynamoDB<br/>DocumentMetadata]
    APIGW[API Gateway<br/>REST API<br/>(API Key Secured)]
    L3[Lambda<br/>get-documents]
    L4[Lambda<br/>get-document-by-id]
    CW[CloudWatch Logs]

    User -->|Upload PDF| S3
    S3 -->|ObjectCreated Event| L1
    L1 -->|PutItem status=UPLOADED| DDB
    L1 -->|Start OCR Job| TX

    EB -->|Scheduled Invoke| L2
    L2 -->|Check Job Status| TX
    L2 -->|UpdateItem status=PROCESSED| DDB

    APIGW -->|GET /documents| L3
    APIGW -->|GET /documents/{documentId}| L4
    L3 --> DDB
    L4 --> DDB
    APIGW -->|JSON Response| User

    L1 --> CW
    L2 --> CW
    L3 --> CW
    L4 --> CW
```

---

## End-to-End Workflow

1. Upload a PDF to the S3 bucket under the uploads/ prefix.
2. S3 triggers the document-ingest Lambda.
3. Lambda stores metadata in DynamoDB and starts Textract.
4. EventBridge triggers a polling Lambda.
5. Poller updates DynamoDB with extracted text.
6. API Gateway exposes secure endpoints for retrieval.

---

## API Endpoints

GET /documents  
GET /documents/{documentId}

---

## Observability

- CloudWatch Logs enabled for API Gateway and Lambda
- Custom access logging configured
- Metrics available for latency and errors

---

## Status

Project is fully complete and production-ready.

