# Event-Driven Serverless File Processing Pipeline (AWS CloudFormation)

## Overview
This project provisions an event-driven serverless file processing pipeline using AWS CloudFormation. When a file is uploaded to an Amazon S3 bucket, an S3 event notification invokes an AWS Lambda function running inside a private subnet within a custom VPC. The Lambda function processes the object, logs metadata back to S3, and sends an alert email via Amazon SNS.

## Architecture
- **VPC & Networking**:
  - 1 Custom VPC (10.0.0.0/16) with DNS resolution enabled
  - 1 Public Subnet (10.0.1.0/24) connected to an Internet Gateway (IGW) and a NAT Gateway
  - 1 Private Subnet (10.0.2.0/24) routing outbound traffic via NAT Gateway
  - S3 Gateway VPC Endpoint for internal private communication
- **Storage**:
  - Amazon S3 bucket with default SSE-S3 encryption and Public Access Block enabled
  - Configured with `s3:ObjectCreated:*` event notification triggering Lambda
- **Compute**:
  - AWS Lambda function (Python 3.9) deployed in the private subnet
  - IAM role with `AWSLambdaVPCAccessExecutionRole`, S3 read/write, and SNS publish permissions
- **Messaging**:
  - Amazon SNS Topic (`FileProcessingAlerts`) with email subscription

## Workflow Flow
1. User uploads a file to the configured S3 bucket.
2. S3 triggers the Lambda function running in the private subnet.
3. The Lambda function reads metadata, writes a log to `processed-logs/` in S3, and publishes an execution alert.
4. Amazon SNS delivers the alert email to the subscribed address.

## Repository Contents
- `template.yaml`: Complete AWS CloudFormation IaC template.
- `architecture-diagram.png`: Architectural diagram detailing network layout and execution flow.
