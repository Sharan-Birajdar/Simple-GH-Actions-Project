# Simple-GH-Actions-Project

📘 South River Mortgage WP
This repository contains the WordPress-based application for the South River Mortgage platform, used to manage website content, user interactions, and mortgage-related workflows.

It is part of a larger ecosystem supporting a mortgage business that specializes in loan origination and financial services. (SignalHire)

🧠 What This Project Is
This project is a WordPress (PHP-based) web application that likely serves as:

🌐 Public-facing mortgage website

🧮 Loan / mortgage tools (calculators, quotes, forms)

📄 Content management system (CMS)

🔗 Integration layer with backend services (APIs, LOS systems)

🎯 Purpose of the Project
The repository is used to:

Manage website content dynamically via WordPress

Provide user-facing mortgage services (quotes, applications)

Integrate with backend systems (loan processing, CRM, etc.)

Deliver scalable, cloud-hosted web infrastructure

🏗️ Tech Stack
🔙 Backend
PHP (WordPress core)

Custom WordPress themes & plugins

🎨 Frontend
HTML, CSS, JavaScript

WordPress templating system

☁️ Cloud & DevOps
AWS (ECS, ECR, CloudFront)

Docker (containerization)

GitHub Actions (CI/CD)

🗄️ Database
MySQL (WordPress default)

📂 Project Structure (Typical)
.
├── wp-content/
│   ├── themes/        # Custom WordPress themes
│   ├── plugins/       # Custom/third-party plugins
│   └── uploads/       # Media files
├── wp-config.php      # WordPress configuration
├── Dockerfile         # Container build config
├── .github/workflows/ # CI/CD pipelines
└── scripts/           # Deployment / utility scripts
🚀 Key Features
🧩 WordPress CMS for content management

🧮 Mortgage tools (quotes, calculators, forms)

🔗 Integration with external APIs/services

⚡ Scalable deployment using AWS

🐳 Docker-based environment

🔄 Automated CI/CD pipeline

🔁 CI/CD Pipeline (Important Part)
This project uses GitHub Actions for automated deployment.

⚙️ Workflow Capabilities
Triggered on push to specific branches (e.g.,
staging
)

Builds Docker image

Pushes image to AWS ECR

Updates ECS service

Invalidates CloudFront cache

🔄 Deployment Flow
Code Push (GitHub)
        ↓
GitHub Actions Trigger
        ↓
Docker Image Build
        ↓
Push to AWS ECR
        ↓
Update ECS Task Definition
        ↓
Deploy to ECS Cluster
        ↓
Invalidate CloudFront Cache
🐳 Docker Usage
The project is containerized for consistent deployment:

Builds Docker image using application code

Uses multi-platform build (e.g., ARM64)

Passes build version as environment variable

☁️ AWS Infrastructure
Key Services Used:
ECR (Elastic Container Registry)
Stores Docker images

ECS (Elastic Container Service)
Runs containers in production

CloudFront
CDN for caching and fast delivery

IAM
Secure access management

📦 Deployment Environments
Staging – Testing environment

(Possibly) Production – Live environment

🔐 Security
AWS credentials stored in GitHub Secrets

No sensitive data in code

Secure deployments via IAM roles

📊 Business Context
This application supports a mortgage company that focuses on:

Loan origination

Reverse mortgages

Financial services operations (SignalHire)

🧪 Use Cases
User visits website → views mortgage info

User submits form → data sent to backend systems

Admin updates content via WordPress dashboard

Dev pushes code → auto deploy via CI/CD

📌 Key Highlights
✅ Full WordPress-based system

✅ Cloud-native deployment (AWS ECS)

✅ Dockerized architecture

✅ Automated CI/CD pipeline

✅ Integrated with mortgage business workflows

🏁 Summary
This repository is a production-ready WordPress application designed for:

Mortgage business websites

Customer interaction and lead generation

Scalable, automated deployment using AWS
