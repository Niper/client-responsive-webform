# Deployment Infrastructure and Documentation

**Agent:** devops_engineer
**Job:** Client Responsive Webform

---

# Deployment Infrastructure and Documentation
## Client Responsive Webform - DevOps Implementation

---

## Executive Summary

This document provides comprehensive infrastructure setup, deployment pipelines, monitoring solutions, and operational documentation for the Client Responsive Webform application serving UK wealth management firms under FCA compliance requirements.

---

## Table of Contents

1. [Infrastructure Architecture](#1-infrastructure-architecture)
2. [CI/CD Pipeline Configuration](#2-cicd-pipeline-configuration)
3. [Security Implementation](#3-security-implementation)
4. [Monitoring and Logging](#4-monitoring-and-logging)
5. [Backup and Disaster Recovery](#5-backup-and-disaster-recovery)
6. [Deployment Guide](#6-deployment-guide)
7. [API Documentation](#7-api-documentation)
8. [Database Schema Documentation](#8-database-schema-documentation)
9. [Security Procedures](#9-security-procedures)
10. [Incident Response Plan](#10-incident-response-plan)
11. [Operational Runbooks](#11-operational-runbooks)
12. [User Guide for Wealth Management Staff](#12-user-guide-for-wealth-management-staff)

---

## 1. Infrastructure Architecture

### 1.1 Cloud Platform: AWS (Recommended)

```
┌─────────────────────────────────────────────────────────────┐
│                         Route 53                             │
│                    (DNS Management)                          │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                    CloudFront CDN                            │
│              (DDoS Protection, SSL/TLS)                      │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                  AWS WAF & Shield                            │
│            (Application Layer Protection)                    │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│              Application Load Balancer                       │
│                  (Multi-AZ, HTTPS)                          │
└──────────┬────────────────────────┬────────────────────────┘
           │                        │
┌──────────▼──────────┐  ┌─────────▼──────────┐
│   ECS Fargate       │  │   ECS Fargate      │
│   (AZ-1)            │  │   (AZ-2)           │
│   - Web App         │  │   - Web App        │
│   - API Service     │  │   - API Service    │
└──────────┬──────────┘  └─────────┬──────────┘
           │                        │
           └────────┬───────────────┘
                    │
┌───────────────────▼────────────────────────────────────────┐
│                    VPC Private Subnet                       │
│  ┌──────────────────┐        ┌──────────────────────┐     │
│  │   RDS PostgreSQL │        │   ElastiCache Redis  │     │
│  │   (Multi-AZ)     │        │   (Session Store)    │     │
│  │   Encrypted      │        │                      │     │
│  └──────────────────┘        └──────────────────────┘     │
└─────────────────────────────────────────────────────────────┘
```

### 1.2 Infrastructure as Code (Terraform)

**File: `infrastructure/terraform/main.tf`**

```hcl
terraform {
  required_version = ">= 1.0"
  
  backend "s3" {
    bucket         = "client-webform-terraform-state"
    key            = "prod/terraform.tfstate"
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
      Project     = "ClientWebform"
      Environment = var.environment
      ManagedBy   = "Terraform"
      Compliance  = "FCA"
      DataClass   = "Sensitive"
    }
  }
}

# VPC Configuration
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"

  name = "client-webform-vpc-${var.environment}"
  cidr = "10.0.0.0/16"

  azs              = ["eu-west-2a", "eu-west-2b", "eu-west-2c"]
  private_subnets  = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets   = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
  database_subnets = ["10.0.201.0/24", "10.0.202.0/24", "10.0.203.0/24"]

  enable_nat_gateway   = true
  enable_vpn_gateway   = false
  enable_dns_hostnames = true
  enable_dns_support   = true

  enable_flow_log                      = true
  create_flow_log_cloudwatch_iam_role  = true
  create_flow_log_cloudwatch_log_group = true
}

# ECS Cluster
resource "aws_ecs_cluster" "main" {
  name = "client-webform-cluster-${var.environment}"

  setting {
    name  = "containerInsights"
    value = "enabled"
  }

  configuration {
    execute_command_configuration {
      logging = "OVERRIDE"
      
      log_configuration {
        cloud_watch_log_group_name = aws_cloudwatch_log_group.ecs_exec.name
      }
    }
  }
}

# RDS PostgreSQL Database
resource "aws_db_instance" "main" {
  identifier     = "client-webform-db-${var.environment}"
  engine         = "postgres"
  engine_version = "15.4"
  instance_class = var.db_instance_class

  allocated_storage     = 100
  max_allocated_storage = 500
  storage_encrypted     = true
  kms_key_id           = aws_kms_key.rds.arn

  db_name  = "clientwebform"
  username = "dbadmin"
  password = random_password.db_password.result

  multi_az               = true
  db_subnet_group_name   = aws_db_subnet_group.main.name
  vpc_security_group_ids = [aws_security_group.rds.id]

  backup_retention_period = 30
  backup_window          = "03:00-04:00"
  maintenance_window     = "mon:04:00-mon:05:00"

  enabled_cloudwatch_logs_exports = ["postgresql", "upgrade"]
  
  deletion_protection = true
  skip_final_snapshot = false
  final_snapshot_identifier = "client-webform-final-${formatdate("YYYY-MM-DD-hhmm", timestamp())}"

  performance_insights_enabled    = true
  performance_insights_kms_key_id = aws_kms_key.rds.arn

  tags = {
    Name = "client-webform-database"
  }
}

# Application Load Balancer
resource "aws_lb" "main" {
  name               = "client-webform-alb-${var.environment}"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb.id]
  subnets            = module.vpc.public_subnets

  enable_deletion_protection = true
  enable_http2              = true
  enable_cross_zone_load_balancing = true

  access_logs {
    bucket  = aws_s3_bucket.alb_logs.id
    prefix  = "alb"
    enabled = true
  }

  drop_invalid_header_fields = true
}

# WAF Configuration
resource "aws_wafv2_web_acl" "main" {
  name  = "client-webform-waf-${var.environment}"
  scope = "REGIONAL"

  default_action {
    allow {}
  }

  # Rate limiting rule
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
      metric_name               = "RateLimitRule"
      sampled_requests_enabled  = true
    }
  }

  # AWS Managed Rules - Core Rule Set
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
      metric_name               = "AWSManagedRulesCommonRuleSetMetric"
      sampled_requests_enabled  = true
    }
  }

  # SQL Injection Protection
  rule {
    name     = "AWSManagedRulesSQLiRuleSet"
    priority = 3

    override_action {
      none {}
    }

    statement {
      managed_rule_group_statement {
        name        = "AWSManagedRulesSQLiRuleSet"
        vendor_name = "AWS"
      }
    }

    visibility_config {
      cloudwatch_metrics_enabled = true
      metric_name               = "AWSManagedRulesSQLiRuleSetMetric"
      sampled_requests_enabled  = true
    }
  }

  visibility_config {
    cloudwatch_metrics_enabled = true
    metric_name               = "client-webform-waf"
    sampled_requests_enabled  = true
  }
}

# CloudFront Distribution
resource "aws_cloudfront_distribution" "main" {
  enabled             = true
  is_ipv6_enabled     = true
  comment             = "Client Webform Distribution"
  default_root_object = "index.html"
  price_class         = "PriceClass_100"
  web_acl_id         = aws_wafv2_web_acl.cloudfront.arn

  aliases = [var.domain_name]

  origin {
    domain_name = aws_lb.main.dns_name
    origin_id   = "ALB"

    custom_origin_config {
      http_port              = 80
      https_port             = 443
      origin_protocol_policy = "https-only"
      origin_ssl_protocols   = ["TLSv1.2"]
    }
  }

  default_cache_behavior {
    allowed_methods  = ["DELETE", "GET", "HEAD", "OPTIONS", "PATCH", "POST", "PUT"]
    cached_methods   = ["GET", "HEAD"]
    target_origin_id = "ALB"

    forwarded_values {
      query_string = true
      headers      = ["Host", "CloudFront-Forwarded-Proto"]

      cookies {
        forward = "all"
      }
    }

    viewer_protocol_policy = "redirect-to-https"
    min_ttl                = 0
    default_ttl            = 3600
    max_ttl                = 86400
    compress               = true
  }

  viewer_certificate {
    acm_certificate_arn      = aws_acm_certificate.main.arn
    ssl_support_method       = "sni-only"
    minimum_protocol_version = "TLSv1.2_2021"
  }

  restrictions {
    geo_restriction {
      restriction_type = "whitelist"
      locations        = ["GB"]  # UK only access
    }
  }

  logging_config {
    include_cookies = true
    bucket          = aws_s3_bucket.cloudfront_logs.bucket_domain_name
    prefix          = "cloudfront/"
  }
}

# KMS Keys for Encryption
resource "aws_kms_key" "rds" {
  description             = "KMS key for RDS encryption"
  deletion_window_in_days = 30
  enable_key_rotation     = true

  tags = {
    Name = "client-webform-rds-key"
  }
}

resource "aws_kms_key" "s3" {
  description             = "KMS key for S3 encryption"
  deletion_window_in_days = 30
  enable_key_rotation     = true

  tags = {
    Name = "client-webform-s3-key"
  }
}

# S3 Buckets for Backups and Logs
resource "aws_s3_bucket" "backups" {
  bucket = "client-webform-backups-${var.environment}"
}

resource "aws_s3_bucket_versioning" "backups" {
  bucket = aws_s3_bucket.backups.id
  
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "backups" {
  bucket = aws_s3_bucket.backups.id

  rule {
    apply_server_side_encryption_by_default {
      kms_master_key_id = aws_kms_key.s3.arn
      sse_algorithm     = "aws:kms"
    }
  }
}

resource "aws_s3_bucket_lifecycle_configuration" "backups" {
  bucket = aws_s3_bucket.backups.id

  rule {
    id     = "backup-retention"
    status = "Enabled"

    transition {
      days          = 30
      storage_class = "STANDARD_IA"
    }

    transition {
      days          = 90
      storage_class = "GLACIER"
    }

    expiration {
      days = 2555  # 7 years for FCA compliance
    }
  }
}
```

**File: `infrastructure/terraform/variables.tf`**

```hcl
variable "environment" {
  description = "Environment name"
  type        = string
  default     = "production"
}

variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "eu-west-2"  # London region for UK compliance
}

variable "domain_name" {
  description = "Domain name for the application"
  type        = string
}

variable "db_instance_class" {
  description = "RDS instance class"
  type        = string
  default     = "db.t4g.large"
}

variable "ecs_task_cpu" {
  description = "CPU units for ECS task"
  type        = number
  default     = 1024
}

variable "ecs_task_memory" {
  description = "Memory for ECS task"
  type        = number
  default     = 2048
}

variable "app_count" {
  description = "Number of application instances"
  type        = number
  default     = 2
}
```

---

## 2. CI/CD Pipeline Configuration

### 2.1 GitHub Actions Workflow

**File: `.github/workflows/deploy.yml`**

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  AWS_REGION: eu-west-2
  ECR_REPOSITORY: client-webform
  ECS_SERVICE: client-webform-service
  ECS_CLUSTER: client-webform-cluster-production

jobs:
  security-scan:
    name: Security Scanning
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'

      - name: OWASP Dependency Check
        uses: dependency-check/Dependency-Check_Action@main
        with:
          project: 'client-webform'
          path: '.'
          format: 'HTML'

  test:
    name: Run Tests
    runs-on: ubuntu-latest
    needs: security-scan
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linting
        run: npm run lint

      - name: Run unit tests
        run: npm run test:unit
        env:
          DATABASE_URL: postgresql://postgres:postgres@localhost:5432/testdb

      - name: Run integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgresql://postgres:postgres@localhost:5432/testdb

      - name: Generate coverage report
        run: npm run test:coverage

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info

  build:
    name: Build and Push Docker Image
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'
    
    outputs:
      image: ${{ steps.build-image.outputs.image }}
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build, tag, and push image to Amazon ECR
        id: build-image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker build \
            --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
            --build-arg VCS_REF=${{ github.sha }} \
            --build-arg VERSION=${{ github.sha }} \
            -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG \
            -t $ECR_REGISTRY/$ECR_REPOSITORY:latest \
            .
          docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
          docker push $ECR_REGISTRY/$ECR_REPOSITORY:latest
          echo "image=$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG" >> $GITHUB_OUTPUT

      - name: Scan Docker image with Trivy
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: ${{ steps.build-image.outputs.image }}
          format: 'sarif'
          output: 'trivy-image-results.sarif'

  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    needs: build
    environment: staging
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Fill in the new image ID in the Amazon ECS task definition
        id: task-def
        uses: aws-actions/amazon-ecs-render-task-definition@v1
        with:
          task-definition: infrastructure/ecs/task-definition-staging.json
          container-name: client-webform
          image: ${{ needs.build.outputs.image }}

      - name: Deploy Amazon ECS task definition
        uses: aws-actions/amazon-ecs-deploy-task-definition@v1
        with:
          task-definition: ${{ steps.task-def.outputs.task-definition }}
          service: client-webform-service-staging
          cluster: client-webform-cluster-staging
          wait-for-service-stability: true

      - name: Run smoke tests
        run: |
          npm run test:smoke -- --url=https://staging.clientwebform.example.com

  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: [build, deploy-staging]
    environment: production
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Fill in the new image ID in the Amazon ECS task definition
        id: task-def
        uses: aws-actions/amazon-ecs-render-task-definition@v1
        with:
          task-definition: infrastructure/ecs/task-definition-production.json
          container-name: client-webform
          image: ${{ needs.build.outputs.image }}

      - name: Deploy Amazon ECS task definition
        uses: aws-actions/amazon-ecs-deploy-task-definition@v1
        with:
          task-definition: ${{ steps.task-def.outputs.task-definition }}
          service: ${{ env.ECS_SERVICE }}
          cluster: ${{ env.ECS_CLUSTER }}
          wait-for-service-stability: true

      - name: Run smoke tests
        run: |
          npm run test:smoke -- --url=https://clientwebform.example.com

      - name: Create deployment notification
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Production deployment completed'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
        if: always()

  rollback:
    name: Rollback Production
    runs-on: ubuntu-latest
    if: failure()
    needs: deploy-production
    environment: production
    
    steps:
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Rollback ECS service
        run: |
          aws ecs update-service \
            --cluster ${{ env.ECS_CLUSTER }} \
            --service ${{ env.ECS_SERVICE }} \
            --force-new-deployment \
            --task-definition $(aws ecs describe-services \
              --cluster ${{ env.ECS_CLUSTER }} \
              --services ${{ env.ECS_SERVICE }} \
              --query 'services[0].deployments[1].taskDefinition' \
              --output text)
```

### 2.2 ECS Task Definition

**File: `infrastructure/ecs/task-definition-production.json`**

```json
{
  "family": "client-webform-production",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "1024",
  "memory": "2048",
  "executionRoleArn": "arn:aws:iam::ACCOUNT_ID:role/ecsTaskExecutionRole",
  "taskRoleArn": "arn:aws:iam::ACCOUNT_ID:role/ecsTaskRole",
  "containerDefinitions": [
    {
      "name": "client-webform",
      "image": "ACCOUNT_ID.dkr.ecr.eu-west-2.amazonaws.com/client-webform:latest",
      "essential": true,
      "portMappings": [
        {
          "containerPort": 3000,
          "protocol": "tcp"
        }
      ],
      "environment": [
        {
          "name": "NODE_ENV",
          "value": "production"
        },
        {
          "name": "PORT",
          "value": "3000"
        }
      ],
      "secrets": [
        {
          "name": "DATABASE_URL",
          "valueFrom": "arn:aws:secretsmanager:eu-west-2:ACCOUNT_ID:secret:client-webform/database-url"
        },
        {
          "name": "JWT_SECRET",
          "valueFrom": "arn:aws:secretsmanager:eu-west-2:ACCOUNT_ID:secret:client-webform/jwt-secret"
        },
        {
          "name": "ENCRYPTION_KEY",
          "valueFrom": "arn:aws:secretsmanager:eu-west-2:ACCOUNT_ID:secret:client-webform/encryption-key"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/client-webform-production",
          "awslogs-region": "eu-west-2",
          "awslogs-stream-prefix": "ecs"
        }
      },
      "healthCheck": {
        "command": ["CMD-SHELL", "curl -f http://localhost:3000/health || exit 1"],
        "interval": 30,
        "timeout": 5,
        "retries": 3,
        "startPeriod": 60
      }
    }
  ]
}
```

---

## 3. Security Implementation

### 3.1 SSL/TLS Configuration

**ACM Certificate Setup:**

```bash
# Request certificate via AWS CLI
aws acm request-certificate \
  --domain-name clientwebform.example.com \
  --subject-alternative-names "*.clientwebform.example.com" \
  --validation-method DNS \
  --region eu-west-2

# Certificate should be validated via DNS records
# ACM will provide CNAME records to add to Route53
```

### 3.2 Security Headers Configuration

**File: `infrastructure/nginx/security-headers.conf`**

```nginx
# Security Headers
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
add_header X-Frame-Options "DENY" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' data:; connect-src 'self'; frame-ancestors 'none';" always;
add_header Permissions-Policy "geolocation=(), microphone=(), camera=()" always;

# Remove server version
server_tokens off;
```

### 3.3 Secrets Management

**AWS Secrets Manager Configuration:**

```bash
# Create database credentials
aws secretsmanager create-secret \
  --name client-webform/database-url \
  --description "Database connection string" \
  --secret-string "postgresql://username:password@endpoint:5432/dbname" \
  --region eu-west-2

# Create JWT secret
aws secretsmanager create-secret \
  --name client-webform/jwt-secret \
  --description "JWT signing secret" \
  --secret-string "$(openssl rand -base64 64)" \
  --region eu-west-2

# Create encryption key
aws secretsmanager create-secret \
  --name client-webform/encryption-key \
  --description "Data encryption key" \
  --secret-string "$(openssl rand -base64 32)" \
  --region eu-west-2

# Enable automatic rotation (90 days)
aws secretsmanager rotate-secret \
  --secret-id client-webform/jwt-secret \
  --rotation-lambda-arn arn:aws:lambda:eu-west-2:ACCOUNT_ID:function:SecretsManagerRotation \
  --rotation-rules AutomaticallyAfterDays=90
```

---

## 4. Monitoring and Logging

### 4.1 CloudWatch Dashboard

**File: `infrastructure/monitoring/cloudwatch-dashboard.json`**

```json
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/ECS", "CPUUtilization", {"stat": "Average"}],
          [".", "MemoryUtilization", {"stat": "Average"}]
        ],
        "period": 300,
        "stat": "Average",
        "region": "eu-west-2",
        "title": "ECS Resource Utilization",
        "yAxis": {
          "left": {
            "min": 0,
            "max": 100
          }
        }
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/ApplicationELB", "TargetResponseTime", {"stat": "Average"}],
          [".", "RequestCount", {"stat": "Sum"}],
          [".", "HTTPCode_Target_4XX_Count", {"stat": "Sum"}],
          [".", "HTTPCode_Target_5XX_Count", {"stat": "Sum"}]
        ],
        "period": 300,
        "stat": "Average",
        "region": "eu-west-2",
        "title": "Application Load Balancer Metrics"
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/RDS", "DatabaseConnections", {"stat": "Average"}],
          [".", "CPUUtilization", {"stat": "Average"}],
          [".", "FreeableMemory", {"stat