# Deployment Guide

This is the primary, streamlined path to deploy the solution. It contains only what you need to get running quickly.

## Context

IroCO2 has been designed to be a SaaS at first and deployed in Ippon AWS account without possibility to self-host it. The Terraform currently present in the repositories is the one we used to maintain the SaaS deployment.

But today, we want to allow self-hosting of the solution. The Terraform and deployment approach currently present is not ideal for this purpose. But we plan to improve it in out next steps. If you want to self-host the solution, you can use the Terraform currently present in the repositories by reading this documentation.

## Prerequisites

- An AWS account
- Terraform
- A domain name within Route53 (or a delegation to Route53)

**_Note: root access to AWS account is not required_**

### Required skills (estimates)
- AWS (console/CLI): intermediate
- Terraform (init/plan/apply, backends, tfvars): intermediate
- DNS/Certificates (Route53, ACM): basic–intermediate
- IAM (roles/policies, least privilege): basic–intermediate
- S3/CloudFront deployment basics: basic–intermediate
- Containers/runtime (selecting image tags, health checks): basic

## Estimated deployment time
 
Summary (elapsed):
- Quick: 1.5–3h
- Typical first-time: 4-6h

Steps (human + AWS wait):
1) AWS prereqs/account: 15–60m
2) Terraform layers (3–5): 1.5–3h total
3) Backend (`iroco2-backend`): 30–60m
4) Frontend (S3/CloudFront): 25–60m (incl. propagation)
5) Lambdas: 25–45m
6) Validation/smoke tests: 20–40m

Notes: Faster with org-ready IAM/Route53 and reusable `tfvars`. Slower with missing permissions, DNS delegation, CloudFront propagation.

## Supported regions

You can deploy IroCO2 in any region supported by AWS. Exception: the client side scanner lambda need to be deployed in `eu-west-3` and the CUR Data Export in `us-east-1`.

## Deployment

1. Clone the [terraform repository](https://github.com/ippontech/iroco2-terraform-modules)
2. Each directory in the `layers` folder is a terraform project. For each : 
    1. Add you backend configuration in the `backend-configs` directory as well as environment configuration in the `env-configs` directory
    2. Initialize the terraform project with `terraform init -backend-config=../backend-configs/<backend>.tfvars -var-file=../env-configs/<env>.tfvars`
    3. Provide all the needed variables in a tfvars file in the `tfvars` directory
    4. Apply the terraform project with `terraform apply -var-file=../tfvars/<env>.tfvars`
3. Clone the [frontend repository](https://github.com/ippontech/iroco2-frontend)
    1. Retrieve the Cloudfront distribution ID and S3 bucket name from the terraform output of the previous repository (`layer/service`)
    2. Follow the README of the frontend repository to deploy the frontend to your cloudfront distribution
4. Clone the [backend repository](https://github.com/ippontech/iroco2-backend)
    1. Add your backend configuration in the `backend-configs` directory as well as environment configuration in the `tfvars` directory (you can choose the backend image version [here](https://github.com/orgs/ippontech/packages?repo_name=iroco2-backend))
    2. Initialize the terraform project with `terraform init -backend-config=../backend-configs/<backend>.tfvars`
    3. Provide all the needed variables in a tfvars file in the `tfvars` directory
        1. You will get the latest version of the docker image of the backend API. You can find them on this page: https://hub.docker.com/r/ippontech/iroco2-backend
    4. Apply the terraform project with `terraform apply -var-file=../tfvars/<env>.tfvars`
5. Clone the lambda repository [iroco2-lambda](https://github.com/ippontech/iroco2-lambdas)


## Backup & Restore

The RDS databases that are created by the terraform modules are automatically backed up using AWS RDS snapshot. If needed you can follow this step by [step guide](https://docs.aws.amazon.com/aws-backup/latest/devguide/restoring-rds.html) from AWS to restore it.

## Secrets and data

RDS database credentials are currently store within AWS Secrets Manager. Rotation is currently disabled.

The secret variables in the backend are injected using Terraform in the ECS Fargate tasks see [backend.tf](https://github.com/ippontech/iroco2-backend/blob/main/tf/)

### Where your data is stored

Your data is stored in the RDS databases that are created by the Terraform modules. The data is stored in the AWS region you choose.

If you use the CUR Analysis and the Scanner, the CUR report will be stored in the S3 bucket within your AWS account. If you choose to self-host IroCO2, no data is exported outside of your AWS account.

## Costs

> **Note:** The costs of running IroCO2 are not covered by Ippon Technologies. You will be responsible for the costs of running the solution.

_We have designed the solution to work only during working hours._

The estimated cost of running IroCO2 in AWS is around $100 per month. This is a rough estimate and can vary based on your specific use case.

There is no licence cost, the software is free to use.

### Billable AWS services (Mandatory vs Optional)

Use this as a quick checklist to estimate costs. “Mandatory” means required for a typical production-like deployment of IroCO2; “Optional” indicates features you can disable if you don’t need them.

| AWS Service | Purpose | Mandatory/Optional | Notes |
| --- | --- | --- | --- |
| Amazon VPC | Network isolation | Mandatory | One VPC with public/private subnets. |
| NAT Gateway | Egress for private subnets | Optional | Use only if private subnets need general internet egress. You can avoid NAT by using VPC endpoints and limiting egress. |
| Elastic Load Balancing (ALB) | Ingress for backend API | Mandatory | If API is public or behind CloudFront. Internal NLB/ALB possible for private-only use. |
| Amazon ECS on Fargate | Run backend containers | Mandatory | Minimum one service for API. |
| Amazon ECR | Container image registry | Optional | You may pull from Docker Hub ippontech/iroco2-backend; using ECR improves reliability and IAM control. |
| Amazon RDS (PostgreSQL) | Application database | Mandatory | Sized per workload; automatic backups recommended. |
| AWS Lambda | Scanner/ETL functions | Optional (per feature) | Mandatory only if you enable the Scanner and/or CUR ingestion functions. |
| Amazon SQS | Asynchronous messaging/decoupling | Optional (per feature) | Used for background processing and decoupling when enabled; costs per requests and polling. |
| Amazon S3 | Frontend hosting, assets, CUR storage | Mandatory | Always required for static frontend hosting; additionally used for CUR if the feature is enabled. |
| Amazon CloudFront | Global CDN for frontend | Mandatory | Serves the UI with TLS and caching. |
| AWS Certificate Manager (ACM) | TLS certificates | Mandatory | Required for CloudFront/ALB HTTPS. |
| Amazon Route 53 | DNS | Mandatory | Public hosted zone or delegated subdomain for your domain. |
| AWS Secrets Manager | Store DB creds and app secrets | Mandatory | Rotation recommended; can also use SSM Parameter Store. |
| AWS Key Management Service (KMS) | Encryption keys | Mandatory | Required to encrypt RDS, S3, Secrets; you may start with AWS-managed keys or use customer-managed keys. |
| Amazon CloudWatch (Logs/Metrics/Alarms) | Observability | Mandatory | Container logs, Lambda logs, dashboards/alarms. |
| AWS Backup | Centralized backups | Optional | RDS snapshots are sufficient for many; AWS Backup adds policy-based mgmt. |
| VPC Endpoints (Gateway/Interface) | Private access to AWS APIs | Mandatory when NAT disabled; otherwise Optional | Required if you disable NAT and keep workloads in private subnets to reach AWS APIs without internet egress. |

Evidence Bookmark: Billable Services List
- Link: docs/deployment/guide.md
- Section: Costs > Billable AWS services (Mandatory vs Optional)
- Paragraph: Table above