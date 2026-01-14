# Serverless Document Processing Pipeline (AWS)

## Overview
This project implements a fully serverless, event-driven document processing system on AWS. When a PDF is uploaded to Amazon S3, the system automatically extracts text using Amazon Textract, stores metadata and results in DynamoDB, and exposes secure REST APIs through Amazon API Gateway.

The solution is scalable, cost-efficient, and production-ready using only managed AWS services.

---

## Architecture Diagram

```mermaid
flowchart TD

    %% 1) INGEST SECTION
    subgraph Ingest ["1) Ingest (Upload → Start OCR)"]
        U((User)) --> S3[S3 Bucket upload]
        S3 --> L1[Lambda document ingest]
    end

    %% 2) ASYNC SECTION
    subgraph Async ["2) Async OCR (Poll until complete)"]
        EB[EventBridge Scheduler] --> L2[Lambda textract poller]
    end

    %% 3) API SECTION
    subgraph API ["3) Query API (Secured REST)"]
        C((Client)) --> APIGW[API Gateway REST API]
        APIGW --> L3[Lambda get documents]
        APIGW --> L4[Lambda get document by id]
    end

    %% SHARED SERVICES
    TX[Amazon Textract OCR]
    DDB[(DynamoDB DocumentMetadata)]
    CW[CloudWatch Logs]

    %% CONNECTIONS
    L1 --> TX
    L1 --> DDB
    L1 --> CW

    L2 --> TX
    L2 --> DDB
    L2 --> CW

    L3 --> DDB
    L3 --> CW
    L4 --> DDB
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
