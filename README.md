# Simple-GH-Actions-Project

## Platform IaC Project Documentation

### 1. Project Overview
This repository is an AWS Infrastructure-as-Code project using Terraform. It provisions the infrastructure required to run the demand-conversions application and includes a separate module for creating Terraform remote state resources.

- Repository name: `platform-iac`
- Main purpose: manage AWS infrastructure for staging and production environments
- Primary technology: Terraform
- Cloud provider: AWS

### 2. Repository Structure
- README.md
  - setup instructions
  - prerequisites
  - Terraform deployment workflow
  - backend configuration guidance
- demand-conversions
  - `staging/`
  - `prod/`
  - each environment has:
    - main.tf
    - variables.tf
    - outputs.tf
    - `terraform.tfvars.example`
    - CHECKLIST.md
    - `modules/`
      - `app/`
      - `sg/`
      - `vpc/`
- tf-state-bucket
  - module to create the Terraform state backend:
    - S3 bucket
    - DynamoDB table

### 3. What This Project Uses
#### Terraform
- `terraform` CLI
- `terraform` version support: `>= 1.4.0`
- AWS provider: `hashicorp/aws`
- Remote backend: AWS S3 + DynamoDB

#### AWS Services
- VPC
- Subnets (public/private)
- VPC Peering
- Security Groups
- Application Load Balancer (ALB)
- ACM certificate
- Route 53 DNS
- EC2 Launch Templates
- Auto Scaling Group
- IAM Role / Instance Profile
- CloudWatch Logs
- S3 buckets
- DynamoDB table

#### Linux & App Provisioning
- Ubuntu package installation
- PHP 8.2 and PHP-FPM
- Nginx
- Composer
- Git
- AWS CLI
- Tesseract OCR
- poppler-utils
- Laravel application deployment

### 4. Architecture Summary
#### Environment Layout
Each environment (`staging`, `prod`) builds:
- VPC/network layer
- security group layer
- application load balancing layer
- compute layer
- DNS configuration

#### VPC Layer
- Uses `terraform-aws-modules/vpc/aws` to create the VPC
- Creates public and private subnets
- Does not enable NAT gateway or VPN gateway
- Creates VPC peering with an existing database VPC
- Adds routes for cross-VPC connectivity:
  - from app VPC to DB VPC
  - from DB VPC back to app VPC

#### Security Layer
- ALB Security Group
  - allows HTTP (80) from anywhere
  - allows HTTPS (443) from anywhere
- App Security Group
  - allows HTTP (80) from the ALB security group
  - allows SSH (22) from anywhere
  - allows outbound DB traffic to the database VPC CIDR and DB SG
  - allows all outbound traffic

Additional rules:
- ALB SG can send all traffic to App SG
- App SG can access DB SG on the specified DB port

#### Application Layer
- ALB with HTTP and HTTPS listeners
  - HTTP listener redirects to HTTPS
  - HTTPS listener uses ACM certificate
- ACM certificate created with DNS validation
- Route 53 record
  - creates alias A record for the application domain pointing to ALB
- S3 bucket for ALB access logs
- CloudWatch log group for application logs

#### Compute Layer
- EC2 launch template
  - uses a custom AMI ID
  - uses a specified instance type
  - associates a public IP
  - applies the app security group
  - includes user-data script
- Auto Scaling Group
  - deploys instances in public subnets
  - attaches to ALB target group
  - uses desired/max/min sizing

### 5. Application Provisioning Flow
The EC2 user-data script does the following:
1. Sets timezone to UTC
2. Updates the system packages
3. Installs common utilities
4. Installs PHP 8.2 and required PHP extensions
5. Installs Composer
6. Installs and configures Nginx
7. Clones the application from GitHub
8. Checks out the configured Git branch
9. Downloads `vendor.zip` from S3 and extracts Laravel dependencies
10. Creates Laravel storage folders and keys
11. Configures `.env` from `.env.example`
12. Writes AWS and app environment variables
13. Configures Nginx for Laravel
14. Restarts Nginx
15. Sets a cron entry for Laravel scheduled tasks

### 6. Remote State Backend
The repo uses an S3 backend with DynamoDB locking. The backend is configured at `terraform init` time and is not hard-coded inside the module.

The backend module tf-state-bucket provisions:
- an S3 bucket with versioning enabled
- a DynamoDB table for Terraform locking
- optional bucket policy code is present but commented out

### 7. Important Variables
Important variables are defined in variables.tf for each env and include:
- `region`
- `environment`
- `application_name`
- `is_production`
- `cidr_block`
- `availability_zones`
- `public_subnets`
- `private_subnets`
- `ami_id`
- `instance_type`
- ASG sizes: `asg_desired`, `asg_max_size`, `asg_min_size`
- `app_domain`
- `route_53_domain`
- GitHub deployment variables:
  - `github_token`
  - `github_repo`
  - `git_branch`
- Laravel app variables:
  - `laravel_app_key`
  - `app_debug`
  - various AWS bucket names
- Database connectivity:
  - `db_connection`
  - `db_host`
  - `db_port`
  - `db_database`
  - `db_username`
  - `db_password`
- DB VPC / DB security group info:
  - `db_vpc_id`
  - `db_sg_id`

### 8. Notes and Observations
- The app module is strongly tied to a Laravel application deployment model.
- It expects external resources:
  - an existing DB VPC
  - a database security group
  - an S3 bucket containing `vendor.zip` and key files
- The ALB is public and routes traffic to app instances in public subnets
- The app instances are configured to pull code from GitHub during boot
- The CHECKLIST.md file in staging is used for Laravel upgrade notes

### 9. Deployment Steps
1. Set up AWS CLI and credentials
2. Choose target environment folder:
   - staging
   - prod
3. Copy `terraform.tfvars.example` to `terraform.tfvars`
4. Fill required variable values
5. Run Terraform init with backend config:
   - `AWS_PROFILE=<profile> terraform init -backend-config="bucket=<bucket>" -backend-config="key=<path>/terraform.tfstate" -backend-config="region=us-east-1" -backend-config="dynamodb_table=<lock-table>"`
6. Run Terraform apply:
   - `AWS_PROFILE=<profile> terraform apply`

### 10. Summary
This repository automates AWS infrastructure provisioning and onboarding for a Laravel-based web application. It includes:
- VPC setup
- secure networking
- public load balancing
- auto-scaling compute
- ACM certificate
- DNS configuration
- Terraform remote state backend setup

> No code or files were created or modified while generating this documentation.
