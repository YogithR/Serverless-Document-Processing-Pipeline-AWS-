# Serverless Document Processing Pipeline (AWS)

## Overview
This project implements a fully serverless, event-driven document processing system on AWS. When a PDF is uploaded to Amazon S3, the system automatically extracts text using Amazon Textract, stores metadata and results in DynamoDB, and exposes secure REST APIs through Amazon API Gateway.

The solution is scalable, cost-efficient, and production-ready using only managed AWS services.

---

## Architecture Diagram

```mermaid
flowchart LR
    %% User & Upload
    User[User]
    S3[S3 Bucket uploads]

    %% Processing
    L1[Lambda document ingest]
    TX[Amazon Textract OCR]
    EB[EventBridge Scheduler]
    L2[Lambda textract poller]

    %% Storage
    DDB[DynamoDB DocumentMetadata]

    %% API Layer
    APIGW[API Gateway REST API secured]
    L3[Lambda get documents]
    L4[Lambda get document by id]

    %% Observability
    CW[CloudWatch Logs]

    %% Upload Flow
    User --> S3
    S3 --> L1
    L1 --> DDB
    L1 --> TX

    %% OCR Processing
    EB --> L2
    L2 --> TX
    L2 --> DDB

    %% API Query Flow
    APIGW --> L3
    APIGW --> L4
    L3 --> DDB
    L4 --> DDB
    APIGW --> User

    %% Logging
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
