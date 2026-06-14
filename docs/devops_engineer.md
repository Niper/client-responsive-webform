# Deployment and Release Management

**Agent:** devops_engineer
**Job:** Client Responsive Webform

---

# Deployment and Release Management Plan
## Client Responsive Webform - Production Deployment

---

## Executive Summary

This document provides a comprehensive deployment and release management plan for the Client Responsive Webform application. The plan encompasses CI/CD pipeline implementation, infrastructure provisioning, security controls, compliance measures, and operational procedures to ensure a secure, reliable production deployment for a UK wealth management firm.

---

## 1. CI/CD Pipeline Implementation

### 1.1 Pipeline Architecture

```yaml
# .github/workflows/ci-cd-pipeline.yml
name: Client Webform CI/CD Pipeline

on:
  push:
    branches: [develop, staging, main]
  pull_request:
    branches: [develop, staging, main]

env:
  NODE_VERSION: '18.x'
  TERRAFORM_VERSION: '1.6.0'

jobs:
  # Code Quality & Security Scanning
  code-quality:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linting
        run: npm run lint
      
      - name: Run type checking
        run: npm run type-check
      
      - name: Run unit tests
        run: npm run test:unit -- --coverage
      
      - name: SonarCloud Scan
        uses: SonarSource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
      
      - name: Upload coverage reports
        uses: codecov/codecov-action@v3

  # Security Vulnerability Scanning
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Run npm audit
        run: npm audit --audit-level=moderate
      
      - name: OWASP Dependency Check
        uses: dependency-check/Dependency-Check_Action@main
        with:
          project: 'client-webform'
          path: '.'
          format: 'HTML'
      
      - name: Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          severity: 'CRITICAL,HIGH'

  # Build Application
  build:
    needs: [code-quality, security-scan]
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build application
        run: npm run build
        env:
          NODE_ENV: production
      
      - name: Create build artifact
        run: tar -czf build-${{ github.sha }}.tar.gz dist/
      
      - name: Upload build artifact
        uses: actions/upload-artifact@v3
        with:
          name: build-artifact
          path: build-${{ github.sha }}.tar.gz
          retention-days: 30

  # Integration Tests
  integration-tests:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Download build artifact
        uses: actions/download-artifact@v3
        with:
          name: build-artifact
      
      - name: Setup test environment
        run: docker-compose -f docker-compose.test.yml up -d
      
      - name: Run integration tests
        run: npm run test:integration
      
      - name: Run E2E tests
        run: npm run test:e2e
      
      - name: Cleanup test environment
        run: docker-compose -f docker-compose.test.yml down

  # Deploy to Staging
  deploy-staging:
    if: github.ref == 'refs/heads/staging'
    needs: integration-tests
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-west-2
      
      - name: Download build artifact
        uses: actions/download-artifact@v3
        with:
          name: build-artifact
      
      - name: Deploy to S3
        run: |
          tar -xzf build-${{ github.sha }}.tar.gz
          aws s3 sync dist/ s3://client-webform-staging --delete
      
      - name: Invalidate CloudFront cache
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ secrets.STAGING_CLOUDFRONT_ID }} \
            --paths "/*"
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=staging

  # Deploy to Production
  deploy-production:
    if: github.ref == 'refs/heads/main'
    needs: integration-tests
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-west-2
      
      - name: Download build artifact
        uses: actions/download-artifact@v3
        with:
          name: build-artifact
      
      - name: Create deployment snapshot
        run: |
          TIMESTAMP=$(date +%Y%m%d-%H%M%S)
          aws s3 sync s3://client-webform-production s3://client-webform-backups/$TIMESTAMP/
      
      - name: Deploy to S3
        run: |
          tar -xzf build-${{ github.sha }}.tar.gz
          aws s3 sync dist/ s3://client-webform-production --delete
      
      - name: Invalidate CloudFront cache
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ secrets.PRODUCTION_CLOUDFRONT_ID }} \
            --paths "/*"
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=production
      
      - name: Notify deployment
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Production deployment completed'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### 1.2 Branch Strategy

```
main (production)
  ↑
staging
  ↑
develop
  ↑
feature/* branches
```

**Branch Policies:**
- `feature/*`: Development work, requires 1 approval
- `develop`: Integration branch, requires 2 approvals + passing tests
- `staging`: Pre-production testing, requires 2 approvals + security scan
- `main`: Production, requires 3 approvals + all checks passing

---

## 2. Infrastructure Configuration

### 2.1 Terraform Infrastructure as Code

```hcl
# terraform/main.tf
terraform {
  required_version = ">= 1.6.0"
  
  backend "s3" {
    bucket         = "client-webform-terraform-state"
    key            = "production/terraform.tfstate"
    region         = "eu-west-2"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
  }
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
  
  default_tags {
    tags = {
      Environment = var.environment
      Project     = "client-webform"
      ManagedBy   = "terraform"
      Compliance  = "FCA"
      DataClass   = "confidential"
    }
  }
}

# S3 Bucket for static hosting
resource "aws_s3_bucket" "webform" {
  bucket = "client-webform-${var.environment}"
}

resource "aws_s3_bucket_public_access_block" "webform" {
  bucket = aws_s3_bucket.webform.id
  
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

resource "aws_s3_bucket_versioning" "webform" {
  bucket = aws_s3_bucket.webform.id
  
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_encryption" "webform" {
  bucket = aws_s3_bucket.webform.id
  
  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
    bucket_key_enabled = true
  }
}

resource "aws_s3_bucket_logging" "webform" {
  bucket = aws_s3_bucket.webform.id
  
  target_bucket = aws_s3_bucket.logs.id
  target_prefix = "s3-access-logs/"
}

resource "aws_s3_bucket_lifecycle_configuration" "webform" {
  bucket = aws_s3_bucket.webform.id
  
  rule {
    id     = "archive-old-versions"
    status = "Enabled"
    
    noncurrent_version_transition {
      noncurrent_days = 30
      storage_class   = "STANDARD_IA"
    }
    
    noncurrent_version_transition {
      noncurrent_days = 90
      storage_class   = "GLACIER"
    }
    
    noncurrent_version_expiration {
      noncurrent_days = 365
    }
  }
}

# CloudFront Distribution
resource "aws_cloudfront_distribution" "webform" {
  enabled             = true
  is_ipv6_enabled     = true
  comment             = "Client Webform ${var.environment}"
  default_root_object = "index.html"
  price_class         = "PriceClass_100"
  
  aliases = [var.domain_name]
  
  origin {
    domain_name = aws_s3_bucket.webform.bucket_regional_domain_name
    origin_id   = "S3-${aws_s3_bucket.webform.id}"
    
    s3_origin_config {
      origin_access_identity = aws_cloudfront_origin_access_identity.webform.cloudfront_access_identity_path
    }
  }
  
  default_cache_behavior {
    allowed_methods  = ["GET", "HEAD", "OPTIONS"]
    cached_methods   = ["GET", "HEAD"]
    target_origin_id = "S3-${aws_s3_bucket.webform.id}"
    
    forwarded_values {
      query_string = false
      
      cookies {
        forward = "none"
      }
    }
    
    viewer_protocol_policy = "redirect-to-https"
    min_ttl                = 0
    default_ttl            = 3600
    max_ttl                = 86400
    compress               = true
  }
  
  custom_error_response {
    error_code         = 404
    response_code      = 200
    response_page_path = "/index.html"
  }
  
  custom_error_response {
    error_code         = 403
    response_code      = 200
    response_page_path = "/index.html"
  }
  
  restrictions {
    geo_restriction {
      restriction_type = "whitelist"
      locations        = ["GB"]
    }
  }
  
  viewer_certificate {
    acm_certificate_arn      = aws_acm_certificate.webform.arn
    ssl_support_method       = "sni-only"
    minimum_protocol_version = "TLSv1.2_2021"
  }
  
  logging_config {
    bucket          = aws_s3_bucket.logs.bucket_domain_name
    prefix          = "cloudfront-logs/"
    include_cookies = false
  }
  
  web_acl_id = aws_wafv2_web_acl.webform.arn
}

# ACM Certificate
resource "aws_acm_certificate" "webform" {
  provider          = aws.us-east-1
  domain_name       = var.domain_name
  validation_method = "DNS"
  
  subject_alternative_names = [
    "www.${var.domain_name}"
  ]
  
  lifecycle {
    create_before_destroy = true
  }
}

# WAF Web ACL
resource "aws_wafv2_web_acl" "webform" {
  name  = "client-webform-${var.environment}"
  scope = "CLOUDFRONT"
  
  default_action {
    allow {}
  }
  
  rule {
    name     = "RateLimitRule"
    priority = 1
    
    action {
      block {}
    }
    
    statement {
      rate_based_statement {
        limit              = 2000
        aggregate_key_type = "IP"
      }
    }
    
    visibility_config {
      cloudwatch_metrics_enabled = true
      metric_name                = "RateLimitRule"
      sampled_requests_enabled   = true
    }
  }
  
  rule {
    name     = "AWSManagedRulesCommonRuleSet"
    priority = 2
    
    override_action {
      none {}
    }
    
    statement {
      managed_rule_group_statement {
        name        = "AWSManagedRulesCommonRuleSet"
        vendor_name = "AWS"
      }
    }
    
    visibility_config {
      cloudwatch_metrics_enabled = true
      metric_name                = "AWSManagedRulesCommonRuleSetMetric"
      sampled_requests_enabled   = true
    }
  }
  
  rule {
    name     = "AWSManagedRulesKnownBadInputsRuleSet"
    priority = 3
    
    override_action {
      none {}
    }
    
    statement {
      managed_rule_group_statement {
        name        = "AWSManagedRulesKnownBadInputsRuleSet"
        vendor_name = "AWS"
      }
    }
    
    visibility_config {
      cloudwatch_metrics_enabled = true
      metric_name                = "AWSManagedRulesKnownBadInputsRuleSetMetric"
      sampled_requests_enabled   = true
    }
  }
  
  visibility_config {
    cloudwatch_metrics_enabled = true
    metric_name                = "webform-waf"
    sampled_requests_enabled   = true
  }
}

# API Gateway for backend
resource "aws_api_gateway_rest_api" "webform_api" {
  name        = "client-webform-api-${var.environment}"
  description = "Client Webform API"
  
  endpoint_configuration {
    types = ["REGIONAL"]
  }
}

# CloudWatch Log Group
resource "aws_cloudwatch_log_group" "api_gateway" {
  name              = "/aws/apigateway/client-webform-${var.environment}"
  retention_in_days = 90
  kms_key_id        = aws_kms_key.logs.arn
}

# KMS Key for encryption
resource "aws_kms_key" "logs" {
  description             = "KMS key for log encryption"
  deletion_window_in_days = 30
  enable_key_rotation     = true
  
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid    = "Enable IAM User Permissions"
        Effect = "Allow"
        Principal = {
          AWS = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:root"
        }
        Action   = "kms:*"
        Resource = "*"
      },
      {
        Sid    = "Allow CloudWatch Logs"
        Effect = "Allow"
        Principal = {
          Service = "logs.${var.aws_region}.amazonaws.com"
        }
        Action = [
          "kms:Encrypt",
          "kms:Decrypt",
          "kms:ReEncrypt*",
          "kms:GenerateDataKey*",
          "kms:CreateGrant",
          "kms:DescribeKey"
        ]
        Resource = "*"
      }
    ]
  })
}

# RDS Aurora for database
resource "aws_rds_cluster" "webform_db" {
  cluster_identifier      = "client-webform-${var.environment}"
  engine                  = "aurora-postgresql"
  engine_version          = "15.3"
  database_name           = "clientwebform"
  master_username         = "dbadmin"
  master_password         = random_password.db_password.result
  backup_retention_period = 30
  preferred_backup_window = "03:00-04:00"
  
  storage_encrypted   = true
  kms_key_id          = aws_kms_key.rds.arn
  
  enabled_cloudwatch_logs_exports = ["postgresql"]
  
  db_subnet_group_name   = aws_db_subnet_group.webform.name
  vpc_security_group_ids = [aws_security_group.rds.id]
  
  deletion_protection = true
  skip_final_snapshot = false
  final_snapshot_identifier = "client-webform-${var.environment}-final-${formatdate("YYYY-MM-DD-hhmm", timestamp())}"
  
  tags = {
    Backup = "required"
  }
}

resource "aws_rds_cluster_instance" "webform_db" {
  count              = 2
  identifier         = "client-webform-${var.environment}-${count.index}"
  cluster_identifier = aws_rds_cluster.webform_db.id
  instance_class     = var.db_instance_class
  engine             = aws_rds_cluster.webform_db.engine
  engine_version     = aws_rds_cluster.webform_db.engine_version
  
  performance_insights_enabled = true
  monitoring_interval          = 60
  monitoring_role_arn          = aws_iam_role.rds_monitoring.arn
}

# Secrets Manager for sensitive data
resource "aws_secretsmanager_secret" "db_credentials" {
  name = "client-webform/${var.environment}/db-credentials"
  
  recovery_window_in_days = 30
}

resource "aws_secretsmanager_secret_version" "db_credentials" {
  secret_id = aws_secretsmanager_secret.db_credentials.id
  secret_string = jsonencode({
    username = aws_rds_cluster.webform_db.master_username
    password = random_password.db_password.result
    host     = aws_rds_cluster.webform_db.endpoint
    port     = aws_rds_cluster.webform_db.port
    database = aws_rds_cluster.webform_db.database_name
  })
}
```

### 2.2 Network Configuration

```hcl
# terraform/network.tf
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = {
    Name = "client-webform-vpc-${var.environment}"
  }
}

resource "aws_subnet" "private" {
  count             = 3
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.${count.index + 1}.0/24"
  availability_zone = data.aws_availability_zones.available.names[count.index]
  
  tags = {
    Name = "client-webform-private-${count.index + 1}"
    Tier = "private"
  }
}

resource "aws_subnet" "public" {
  count             = 3
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.${count.index + 101}.0/24"
  availability_zone = data.aws_availability_zones.available.names[count.index]
  
  map_public_ip_on_launch = true
  
  tags = {
    Name = "client-webform-public-${count.index + 1}"
    Tier = "public"
  }
}

resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
  
  tags = {
    Name = "client-webform-igw"
  }
}

resource "aws_nat_gateway" "main" {
  count         = 3
  allocation_id = aws_eip.nat[count.index].id
  subnet_id     = aws_subnet.public[count.index].id
  
  tags = {
    Name = "client-webform-nat-${count.index + 1}"
  }
}

resource "aws_eip" "nat" {
  count  = 3
  domain = "vpc"
  
  tags = {
    Name = "client-webform-nat-eip-${count.index + 1}"
  }
}

# Security Groups
resource "aws_security_group" "rds" {
  name        = "client-webform-rds-${var.environment}"
  description = "Security group for RDS database"
  vpc_id      = aws_vpc.main.id
  
  ingress {
    from_port       = 5432
    to_port         = 5432
    protocol        = "tcp"
    security_groups = [aws_security_group.lambda.id]
    description     = "PostgreSQL from Lambda"
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_security_group" "lambda" {
  name        = "client-webform-lambda-${var.environment}"
  description = "Security group for Lambda functions"
  vpc_id      = aws_vpc.main.id
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# VPC Flow Logs
resource "aws_flow_log" "main" {
  iam_role_arn    = aws_iam_role.flow_logs.arn
  log_destination = aws_cloudwatch_log_group.flow_logs.arn
  traffic_type    = "ALL"
  vpc_id          = aws_vpc.main.id
}

resource "aws_cloudwatch_log_group" "flow_logs" {
  name              = "/aws/vpc/client-webform-${var.environment}"
  retention_in_days = 90
  kms_key_id        = aws_kms_key.logs.arn
}
```

---

## 3. Monitoring and Logging

### 3.1 CloudWatch Dashboard Configuration

```json
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/CloudFront", "Requests", { "stat": "Sum", "label": "Total Requests" }],
          [".", "BytesDownloaded", { "stat": "Sum", "label": "Bytes Downloaded" }],
          [".", "4xxErrorRate", { "stat": "Average", "label": "4xx Error Rate" }],
          [".", "5xxErrorRate", { "stat": "Average", "label": "5xx Error Rate" }]
        ],
        "view": "timeSeries",
        "stacked": false,
        "region": "us-east-1",
        "title": "CloudFront Metrics",
        "period": 300
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/ApiGateway", "Count", { "stat": "Sum" }],
          [".", "Latency", { "stat": "Average" }],
          [".", "4XXError", { "stat": "Sum" }],
          [".", "5XXError", { "stat": "Sum" }]
        ],
        "view": "timeSeries",
        "stacked": false,
        "region": "eu-west-2",
        "title": "API Gateway Metrics",
        "period": 300
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/RDS", "CPUUtilization", { "stat": "Average" }],
          [".", "DatabaseConnections", { "stat": "Average" }],
          [".", "ReadLatency", { "stat": "Average" }],
          [".", "WriteLatency", { "stat": "Average" }]
        ],
        "view": "timeSeries",
        "stacked": false,
        "region": "eu-west-2",
        "title": "RDS Performance",
        "period": 300
      }
    },
    {
      "type": "log",
      "properties": {
        "query": "SOURCE '/aws/lambda/client-webform-submit'\n| fields @timestamp, @message\n| filter @message like /ERROR/\n| sort @timestamp desc\n| limit 20",
        "region": "eu-west-2",
        "title": "Recent Errors",
        "stacked": false
      }
    }
  ]
}
```

### 3.2 CloudWatch Alarms

```hcl
# terraform/monitoring.tf
resource "aws_cloudwatch_metric_alarm" "high_error_rate" {
  alarm_name          = "client-webform-high-error-rate-${var.environment}"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = "2"
  metric_name         = "5XXError"
  namespace           = "AWS/ApiGateway"
  period              = "300"
  statistic           = "Sum"
  threshold           = "10"
  alarm_description   = "This metric monitors API Gateway 5xx errors"
  alarm_actions       = [aws_sns_topic.alerts.arn]
  
  dimensions = {
    ApiName = aws_api_gateway_rest_api.webform_api.name
  }
}

resource "aws_cloudwatch_metric_alarm" "high_latency" {
  alarm_name          = "client-webform-high-latency-${var.environment}"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = "2"
  metric_name         = "Latency"
  namespace           = "AWS/ApiGateway"
  period              = "300"
  statistic           = "Average"
  threshold           = "3000"
  alarm_description   = "This metric monitors API Gateway latency"
  alarm_actions       = [aws_sns_topic.alerts.arn]
  
  dimensions = {
    ApiName = aws_api_gateway_rest_api.webform_api.name
  }
}

resource "aws_cloudwatch_metric_alarm" "db_cpu_high" {
  alarm_name          = "client-webform-db-cpu-high-${var.environment}"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = "2"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/RDS"
  period              = "300"
  statistic           = "Average"
  threshold           = "80"
  alarm_description   = "Database CPU utilization is too high"
  alarm_actions       = [aws_sns_topic.alerts.arn]
  
  dimensions = {
    DBClusterIdentifier = aws_rds_cluster.webform_db.cluster_identifier
  }
}

resource "aws_cloudwatch_metric_alarm" "db_connections_high" {
  alarm_name          = "client-webform-db-connections-high-${var.environment}"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = "1"
  metric_name         = "DatabaseConnections"
  namespace           = "AWS/RDS"
  period              = "300"
  statistic           = "Average"
  threshold           = "80"
  alarm_description   = "Database connection count is too high"
  alarm_actions       = [aws_sns_topic.alerts.arn]
  
  dimensions = {
    DBClusterIdentifier = aws_rds_cluster.webform_db.cluster_identifier
  }
}

resource "aws_sns_topic" "alerts" {
  name              = "client-webform-alerts-${var.environment}"
  kms_master_key_id = aws_kms_key.sns.id
}

resource "aws_sns_topic_subscription" "alerts_email" {
  topic_arn = aws_sns_topic.alerts.arn
  protocol  = "email"
  endpoint  = var.alert_email
}

resource "aws_sns_topic_subscription" "alerts_slack" {
  topic_arn = aws_sns_topic.alerts.arn
  protocol  = "https"
  endpoint  = var.slack_webhook_url
}
```

### 3.3 Application Logging Configuration

```typescript
// src/utils/logger.ts
import { CloudWatchLogsClient, PutLogEventsCommand } from '@aws-sdk/client-cloudwatch-logs';

export enum LogLevel {
  DEBUG = 'DEBUG',
  INFO = 'INFO',
  WARN = 'WARN',
  ERROR = 'ERROR',
  CRITICAL = 'CRITICAL'
}

interface LogEntry {
  timestamp: string;
  level: LogLevel;
  message: string;
  context?: Record<string, any>;
  userId?: string;
  sessionId?: string;
  requestId?: string;
  environment: string;
}

class Logger {
  private cloudWatchClient: CloudWatchLogsClient;
  private logGroupName: string;
  private logStreamName: string;
  private buffer: LogEntry[] = [];
  private flushInterval: NodeJS.Timeout;

  constructor() {
    this.cloudWatchClient = new CloudWatchLogsClient({ region: process.env.AWS_REGION });
    this.logGroupName = process.env.LOG_GROUP_NAME || '/aws/client-webform';
    this.logStreamName = `${process.env.ENVIRONMENT}-${Date.now()}`;
    
    // Flush logs every 5 seconds
    this.flushInterval = setInterval(() => this.flush(), 5000);
  }

  private createLogEntry(level: LogLevel, message: string, context?: Record<string, any>): LogEntry {
    return {
      timestamp: new Date().to