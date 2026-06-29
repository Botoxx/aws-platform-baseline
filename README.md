# aws-platform-baseline

Production-ready multi-account AWS landing zone, built with Terraform.  
Designed for SaaS companies scaling from single-account chaos to governed, cost-aware infrastructure.

## What this solves

Most AWS accounts accumulate technical debt fast: no tagging standards, flat IAM, no cost alerts, everything in one account. This baseline gives you a clean foundation before that debt compounds.

## Architecture

```
management-account/
  ├── org-policies/          # SCPs — deny root usage, enforce regions, require MFA
  ├── iam/                   # Cross-account roles, SSO permission sets
  └── billing-alerts/        # Cost anomaly detection, budget alarms

shared-services-account/
  ├── networking/            # Transit Gateway, shared VPC, DNS
  └── security/              # GuardDuty master, CloudTrail, Config aggregator

workload-accounts/
  ├── dev/
  ├── staging/
  └── prod/
      └── vpc/               # Per-environment VPC with private/public/data subnets
```

## Key design decisions

- **Remote state:** S3 backend + DynamoDB locking per account
- **Module structure:** DRY — environments consume modules, no copy-paste
- **Tagging:** Enforced via variables; `Environment`, `Owner`, `CostCenter` required on all resources
- **Security baseline:** GuardDuty, CloudTrail, AWS Config enabled by default in every account
- **Cost controls:** AWS Cost Anomaly Detection + budget alarms wired to SNS on day 1

## Usage

```bash
# Bootstrap management account
cd management-account
terraform init
terraform apply

# Deploy shared services
cd ../shared-services-account
terraform init -backend-config=backend.hcl
terraform apply

# Deploy workload account (repeat per environment)
cd ../workload-accounts/prod
terraform init -backend-config=backend.hcl
terraform apply
```

## Prerequisites

- AWS Organizations enabled
- Terraform >= 1.6
- AWS CLI configured with management account credentials

## Status

🚧 Work in progress — foundational modules complete, workload account templates in progress.

---

*Part of the [Apex Lab](https://github.com/Botoxx) cloud engineering portfolio.*
