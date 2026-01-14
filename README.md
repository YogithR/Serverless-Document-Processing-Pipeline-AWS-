# Serverless Document Processing Pipeline (AWS)

## Overview
This project implements a fully serverless, event-driven document processing system on AWS. When a PDF is uploaded to Amazon S3, the system automatically extracts text using Amazon Textract, stores metadata and results in DynamoDB, and exposes secure REST APIs through Amazon API Gateway.

The solution is scalable, cost-efficient, and production-ready using only managed AWS services.

---

## Architecture Diagram

```mermaid
flowchart LR

    %% Vertical logic for the main processes
    subgraph Processes
        direction TB
        subgraph Ingest ["1) Ingest"]
            U((User)) --> S3[S3 Bucket upload]
            S3 --> L1[Lambda: Ingest]
        end

        subgraph Async ["2) Async OCR"]
            EB[EventBridge] --> L2[Lambda: Poller]
        end

        subgraph API ["3) Query API"]
            C((Client)) --> APIGW[API Gateway]
            APIGW --> L3[Lambda: Get Docs]
            APIGW --> L4[Lambda: Get ID]
        end
    end

    %% Shared Services moved to the right to stop line crossing
    subgraph Shared ["Shared Services"]
        direction TB
        TX[Amazon Textract OCR]
        DDB[(DynamoDB Metadata)]
        CW[CloudWatch Logs]
    end

    %% Clear, non-crossing horizontal connections
    L1 & L2 --> TX
    L1 & L2 & L3 & L4 --> DDB
    L1 & L2 & L3 & L4 --> CW
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
