# Deployment Guide

This is the primary, streamlined path to deploy the solution. It contains only what you need to get running quickly.

## Context

IroCO2 has been designed to be a SaaS at first and deployed in Ippon AWS account without possibility to self-host it. The Terraform currently present in the repositories is the one we used to maintain the SaaS deployment.

But today, we want to allow self-hosting of the solution. The Terraform and deployment approach currently present is not ideal for this purpose. But we plan to improve it in out next steps. If you want to self-host the solution, you can use the Terraform currently present in the repositories by reading this documentation.



## Prerequisites

- An AWS account
- Terraform
- A domain name within Route53 (or a delegation to Route53)

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

## Deployment

1. Clone the [terraform repository](https://github.com/ippontech/iroco2-terraform-modules)
2. Each directory in the `layers` folder is a terraform project. For each : 
    1. Add you backend configuration in the `backend-configs` directory as well as environment configuration in the `env-configs` directory
    2. Init the terraform project with `terraform init -backend-config=../backend-configs/<backend>.tfvars -var-file=../env-configs/<env>.tfvars`
    3. Provide all the needed variables in a tfvars file in the `tfvars` directory
    4. Apply the terraform project with `terraform apply -var-file=../tfvars/<env>.tfvars`
3. Clone the [frontend repository](https://github.com/ippontech/iroco2-frontend)
    1. Retrive the Cloudfront distribution ID and S3 bucket namefrom the terraform output of the previous repository (`layer/service`)
    2. Follow the README of the frontend repository to deploy the frontend to your cloudfront distribution
4. Clone the [backend repository](https://github.com/ippontech/iroco2-backend)
    1. Add your backend configuration in the `backend-configs` directory as well as environment configuration in the `tfvars` directory (you can choose the backend image version [here](https://github.com/orgs/ippontech/packages?repo_name=iroco2-backend))
    2. Init the terraform project with `terraform init -backend-config=../backend-configs/<backend>.tfvars`
    3. Provide all the needed variables in a tfvars file in the `tfvars` directory
        1. You will the get the latest version of the docker image of the backend API. You can find them on this page: https://hub.docker.com/r/ippontech/iroco2-backend
    4. Apply the terraform project with `terraform apply -var-file=../tfvars/<env>.tfvars`
5. Clone the lambda repository [iroco2-lambda](https://github.com/ippontech/iroco2-lambdas)
    
    
    
    