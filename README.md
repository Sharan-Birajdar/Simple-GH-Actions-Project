# Simple-GH-Actions-Project


## Demand Conversions — Repo Overview

This repository is a Laravel-based PHP web application named **Demand Conversions**. It appears to be a production-grade platform with AWS-based deployment, external service integrations, and a mixed server-rendered / JavaScript frontend.

---

## 1. What this project is

- A **Laravel 10** application running on **PHP ^8.2**
- Uses standard Laravel structure: app, bootstrap, config, database, resources, routes, public
- Likely supports CRM/mortgage/vendor workflows, given packages and docs naming:
  - DocuSign
  - FedEx
  - Twilio
  - Microsoft Azure / Zoho auth
  - Mailchimp
  - PDF generation and merging
  - Excel/Word exports
- Main entry points:
  - index.php
  - artisan CLI
  - server.php

---

## 2. Key technologies used

### Backend
- PHP 8.2
- Laravel Framework 10
- Composer dependency management
- MySQL / relational database
- Laravel queue workers / Supervisor
- AWS SDK for PHP
- Sentry for error tracking
- Doctrine DBAL for database migrations/alterations

### Frontend
- `esbuild` bundling
- `@tiptap/*` rich text editor packages
- `tippy.js` tooltips
- Minimal frontend JS defined in package.json:
  - `build:sales-notes` builds sales-notes-editor.js

### Integrations and libraries
- AWS: S3, ELB, Auto Scaling, CloudWatch, SSM
- DocuSign: `tucker-eric/laravel-docusign`
- FedEx API wrapper
- Twilio SDK
- Social auth providers: Microsoft Azure, Zoho
- PDF libraries: Dompdf, FPDI, TCPDF, PDF merger tools
- Excel/Word: `maatwebsite/excel`, `phpoffice/phpspreadsheet`, `phpoffice/phpword`
- HTML sanitization: `ezyang/htmlpurifier`
- Custom/private package: `south-river-mtg/laravel-extensions`

---

## 3. CI / CD workflow

### GitHub Actions workflows
Stored in workflows:
- ci.yml
- deploy-app-production.yml
- deploy-staging.yml
- terraform-aws-deploy.yml

### CI (ci.yml)
- Triggers on pull requests to `master-upgraded` and `staging`
- Steps:
  1. Checkout
  2. Setup PHP 8.2 with required extensions
  3. Cache Composer vendor directory
  4. Install dependencies
  5. Copy .env.example → .env
  6. Run `php artisan key:generate`
  7. Run `php artisan test`

### Staging deployment (deploy-staging.yml)
- Triggers on push to `staging`
- Uses OIDC role assumption for AWS credentials
- Deploys via AWS SSM to a single staging instance
- Runs `.deploy/ubuntu/deploy-app.sh staging <sha> ...`

### Production deployment (deploy-app-production.yml)
- Triggers on push to `master-upgraded`
- Uses AWS credentials from repo vars/secrets
- Queries Auto Scaling Group for `InService` instance IDs
- Deploys sequentially to each instance via SSM
- Verifies the deploy status, stops if any instance fails
- Optionally checks ALB target health after deploy

### Terraform infrastructure workflow (terraform-aws-deploy.yml)
- Manual workflow dispatch only
- Checks out an external repo: `south-river-mtg/platform-iac`
- Runs Terraform in `terraform/demand-conversions/prod`
- Supports `plan_only` mode and apply
- Uses many environment vars and secrets for AWS/Terraform config

---

## 4. Deploy process

Main deploy script: deploy-app.sh

### What it does
- Fetches latest code from Git
- Checks out the branch / commit
- Runs `composer install --no-dev --optimize-autoloader`
- Runs `php artisan migrate --force`
- Sets Laravel file permissions
- For staging: runs `setup-redis.sh`
- Runs `post-deploy.sh` when CloudWatch log group is provided
- Clears Laravel caches
- Reloads `php8.2-fpm`
- Restarts Supervisor queue workers
- Performs a health check against `http://localhost/health-check`
- If health check fails, performs rollback to previous commit

### Helper scripts
- functions.sh
  - file permission helpers
  - supervisor install/config helpers
  - SSH tunnel helper
  - ODBC driver install helper
- post-deploy.sh
  - handles cache clearing, CloudWatch config, permissions, and related post-deploy tasks
- setup-redis.sh
  - starts a Docker Redis container for staging
- update-env-cloudwatch.sh
  - likely updates CloudWatch config based on .env

---

## 5. Local setup / how to use

### Required files
The repo expects additional deployment/setup assets from SharePoint:
- `db_dc_staging.gz`
- auth.json
- `docusign-keys.zip`

### Basic local setup flow
1. Clone repo
2. Switch to branch: `master-upgraded`
3. Copy auth.json to repo root
4. Extract DocuSign keys into keys
5. Copy .env.example to .env
6. Update DB and app config values in .env
7. Run:
   - `composer install`
   - `php artisan key:generate`
   - `php artisan migrate`
8. Set permissions:
   - `chmod -R 775 storage bootstrap/cache`
9. Build frontend asset if needed:
   - `npm install` / or just run `npm exec esbuild ...` for `build:sales-notes`
   - `npm run build:sales-notes`

### Notes from repo
- SETUP_STATUS.md documents local environment progress and blockers
- `composer install` can fail without auth.json because `setasign` packages require auth
- package.json contains only frontend build tooling for one editor asset

---

## 6. Repo structure highlights

Important directories:
- app — Laravel app code, models, controllers, services, modules
- bootstrap — framework bootstrap
- config — Laravel config files
- database — migrations, seeders, factories
- public — web root
- resources — views, assets, JS source
- routes — `web.php`, `api.php`
- storage — logs, cache, keys, uploads
- tests — test cases
- ubuntu — deployment scripts
- workflows — CI/CD definitions
- docs — project/feature docs

Key files:
- readme.md
- SETUP_STATUS.md
- composer.json
- package.json
- .env.example
- ci.yml
- deploy-app-production.yml
- deploy-staging.yml
- terraform-aws-deploy.yml
- deploy-app.sh

---

## 7. How it works end-to-end

1. Developers work on Laravel code in app, resources, and routes
2. Pull requests are validated via GitHub Actions CI on `master-upgraded` and `staging`
3. Pushing to `staging` deploys to staging EC2 via SSM
4. Pushing to `master-upgraded` deploys to production ASG instances one by one
5. Deploy script updates code, runs migrations, clears caches, and validates health
6. Production infrastructure config is managed in an external Terraform repo via manual workflow dispatch

---

## 8. Practical next steps

If you want, I can also produce:
- a single consolidated README.md update for this repo
- a simplified architecture diagram
- a one-page “developer onboarding” doc
- a list of missing docs / cleanup items in this repo
