# Serverless Document Processing Pipeline (AWS)

## Overview
This project implements a fully serverless, event-driven document processing system on AWS. When a PDF is uploaded to Amazon S3, the system automatically extracts text using Amazon Textract, stores metadata and results in DynamoDB, and exposes secure REST APIs through Amazon API Gateway.

The solution is scalable, cost-efficient, and production-ready using only managed AWS services.

---

## Architecture Diagram

```mermaid
%%{init: {"flowchart": {"nodeSpacing": 25, "rankSpacing": 25, "curve": "basis"}, "themeVariables": {"fontSize": "12px"}} }%%
flowchart TB

subgraph S1["1) Ingest (Upload → Start OCR)"]
direction LR
U["User"] --> S3["S3 (uploads/)"]
S3 --> L1["Lambda: document-ingest-handler"]
L1 -->|"Put item"| DDB["DynamoDB: DocumentMetadata"]
L1 -->|"Start job"| TX["Amazon Textract (Async OCR)"]
L1 --> CW["CloudWatch Logs"]
end

subgraph S2["2) Async OCR (Poll until complete)"]
direction LR
EB["EventBridge Scheduler"] --> L2["Lambda: textract-poller"]
L2 -->|"Check status"| TX
L2 -->|"Update item"| DDB
L2 --> CW
end

subgraph S3API["3) Query API (Secured REST)"]
direction LR
C["Client"] --> APIGW["API Gateway (API Key)"]
APIGW -->|"GET /documents"| L3["Lambda: get-documents"]
APIGW -->|"GET /documents/{documentId}"| L4["Lambda: get-document-by-id"]
L3 --> DDB
L4 --> DDB
L3 --> CW
L4 --> CW
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
