# Serverless Document Processing Pipeline (AWS)

## Overview
This project implements a fully serverless, event-driven document processing system on AWS. When a PDF is uploaded to Amazon S3, the system automatically extracts text using Amazon Textract, stores metadata and results in DynamoDB, and exposes secure REST APIs through Amazon API Gateway.

The solution is scalable, cost-efficient, and production-ready using only managed AWS services.

---

## Architecture Diagram

```mermaid
flowchart LR

subgraph ING["1) Ingest (Upload -> Start OCR)"]
direction TB
U[User] --> S3["S3 Bucket: uploads/"]
S3 --> L1["Lambda: document-ingest-handler"]
L1 --> DDB1["DynamoDB: DocumentMetadata"]
L1 --> TX1["Amazon Textract OCR"]
L1 --> CW1["CloudWatch Logs"]
end

subgraph ASYNC["2) Async OCR (Poll until complete)"]
direction TB
EB["EventBridge Scheduler"] --> L2["Lambda: textract-poller"]
L2 --> TX2["Amazon Textract OCR"]
L2 --> DDB2["DynamoDB: DocumentMetadata"]
L2 --> CW2["CloudWatch Logs"]
end

subgraph API["3) Query API (Secured REST)"]
direction TB
C[Client] --> APIGW["API Gateway REST API"]
APIGW --> L3["Lambda: get-documents"]
APIGW --> L4["Lambda: get-document-by-id"]
L3 --> DDB3["DynamoDB: DocumentMetadata"]
L4 --> DDB3
L3 --> CW3["CloudWatch Logs"]
L4 --> CW3
end

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
