# Simple-GH-Actions-Project

## Reverse Pilot Repository Documentation

### Repository Overview
Reverse Pilot is a mortgage/loan processing platform with a Django backend and a Vue 3 frontend. It supports loan creation, compliance workflows, integration with external systems, and document generation.

### What the Project Is For
- Integrates with DC loan origination systems to create or update loans.
- Manages loan officer assignment and user-linked loan creation.
- Handles loan workflow steps including underwriting, compliance, and closing.
- Supports document generation, PDF processing, and title/credit/FHA integrations.

### Key Technologies

#### Backend (dc_los_django)
- Python 3.x
- Django 5
- Django REST Framework
- OAuth2 via `django-oauth-toolkit`
- Celery task queue
- Redis support (`redis`, `django-redis`)
- PostgreSQL production datastore
- SQLite for local development
- AWS S3 support via `django-storages`
- PDF processing:
  - WeasyPrint
  - PyPDF2 / pypdf
  - `pdf2image`, `pytesseract`
- Elasticsearch
- OpenAI integration
- Excel and CSV processing:
  - `pandas`
  - `openpyxl`
  - `xlsxwriter`
  - `papaparse`
- Image and OCR support:
  - `opencv-python`
- Security and cryptography:
  - `cryptography`
  - `PyCryptodome`

#### Frontend (reverse-pilot-frontend)
- Vue 3
- Vue Router 4
- Vuex 4
- Tailwind CSS
- Vue CLI + Vite
- Axios for API calls
- Charts with `apexcharts`, `chart.js`, `vue-chartjs`
- PDF embedding with `vue-pdf-embed`
- Rich text editor via TinyMCE
- File parsing via `papaparse` and `xlsx`
- FontAwesome icons

### Project Structure

This repository has two main application roots:

## 1. dc_los_django (Backend - Django)

Backend Django project

### Structure

- **apps/**  
  Contains Django apps like `company`, `core`, `loan`, etc.

- **dc_los_django/**  
  Contains Django settings, `wsgi.py`, `celery.py`, authentication, logging, and app entrypoints

- **templates/**, **static/**, **documents/**, **logs/**, **scripts/**  
  Support the backend

- **.github/workflows/**  
  Contains CI/CD workflows for backend deployment and checks

- **requirements.txt**, **pytest.ini**, **setup.md**, **AGENTS.md**, **CLAUDE.md**, **mkdocs.yml**  
  Documentation and setup/config files

- **manage.py**  
  Django CLI entrypoint


## 2. reverse-pilot-frontend (Frontend - Vue.js)

Vue.js frontend application

### Structure

- **package.json**, **package-lock.json**  
  Dependency management

- **src/**  
  Vue source files

- **public/**  
  Static web assets

- **.github/workflows/**  
  Frontend deployment workflows

- **README.md**  
  Includes npm setup and build commands


## 📂 Other Supporting Directories

- **docs/**  
  Documentation and architecture notes

- **scripts/**  
  Project utilities

- **static/** and **templates/**  
  Shared web/static content


### CI/CD Workflow
GitHub Actions workflows exist under workflows.

Optimizing tool selection...Searching workspace for deployment-related configuration files.

Searched for files matching `**/*deploy*`, 7 matches

Found the deployment workflow files; next I’m locating all likely deployment-related files so the descriptions cover the relevant set.

Searched for files matching `**/gunicorn*.py`, 2 matches

Searched for files matching `**/*.sh`, 2 matches

Searched for files matching `**/*Docker*`, no matches

Searched for files matching `**/*.yml`, 14 matches

I’ve identified six deployment workflows plus a couple of runtime deploy support files; now I’ll inspect them for concise summaries.

Read [](file:///d%3A/Projects/Reverse_pilot/reverse-pilot-frontend/.github/workflows/dev-deploy.yml#1-1), lines 1 to 120

Read [](file:///d%3A/Projects/Reverse_pilot/reverse-pilot-frontend/.github/workflows/beta-deploy.yml)

Read [](file:///d%3A/Projects/Reverse_pilot/dc_los_django/.github/workflows/dev_deploy.yml#1-1), lines 1 to 120

Read [](file:///d%3A/Projects/Reverse_pilot/reverse-pilot-frontend/.github/workflows/prod-deploy.yml#1-1), lines 1 to 120

Read [](file:///d%3A/Projects/Reverse_pilot/dc_los_django/.github/workflows/prod_deploy.yml#1-1), lines 1 to 120

I’ve captured the main deployment workflows; now I’ll inspect docs deployment and runtime startup files for exact purpose.

Read [](file:///d%3A/Projects/Reverse_pilot/dc_los_django/gunicorn.conf.py#1-1), lines 1 to 120

Read [](file:///d%3A/Projects/Reverse_pilot/dc_los_django/gunicorn_sse.conf.py)

Read [](file:///d%3A/Projects/Reverse_pilot/dc_los_django/.github/workflows/docs_deploy.yml#1-1), lines 1 to 120

Read [](file:///d%3A/Projects/Reverse_pilot/dc_los_django/start-celery.sh)

Read [](file:///d%3A/Projects/Reverse_pilot/dc_los_django/start-dev.sh#1-1), lines 1 to 120

## Deployment files and short descriptions

### prod-deploy.yml
- Builds the Vue frontend, creates a production `.env`, and packages the dist output.
- Uploads the build to an S3 `prod/current` bucket path, saves a versioned backup, and deploys via AWS SSM.

### dev-deploy.yml
- Runs on merges/pushes to `dev`, installs Node dependencies, builds the Vue app, and validates built assets.
- Creates a deployment manifest and prepares the dev build for deployment, with extra integrity checks.

### beta-deploy.yml
- Deploys the beta frontend branch by building the app locally and copying `dist/` over SSH to a beta server.
- Restarts Nginx on the remote host after upload, using beta-specific secrets and SSH key authentication.

### prod_deploy.yml
- Production Django deploy pipeline that first snapshots RDS for a pre-migration backup and enforces snapshot retention.
- Continues with database migrations and deploy steps against the prod environment via AWS credentials/SSM.

### dev_deploy.yml
- Deploys the Django app for `dev` by sending an AWS SSM command to the EC2 host.
- Syncs code, installs Python requirements, ensures dependencies, and updates the dev instance in-place.

### docs_deploy.yml
- Builds MkDocs documentation from the docs branch and deploys the generated site to a docs web directory.
- Uses AWS SSM to run the deployment script on EC2, updates static docs files, and reloads Nginx.

## Runtime deployment support files

### gunicorn.conf.py
- Gunicorn configuration for the Django app tuned for JVM-aware workloads with controlled worker counts and lifecycle hooks.
- Sets production logging, max-requests, preload, and JVM-friendly worker behavior.

### gunicorn_sse.conf.py
- Gunicorn config for SSE handling with `gevent`, long timeouts, and persistent connections.
- Designed for a separate SSE service endpoint on port `8001`.

### start-dev.sh
- Local development launcher that starts/stops backend and frontend services together, with PID tracking and log management.
- Includes environment setup and health checks for a local dev workflow.

### start-celery.sh
- Starts the Django Celery worker against the project virtualenv and configured queues.
- Provides a simple command entrypoint for background task processing on the host.

### Setup Summary

#### Backend
1. Create and activate Python virtual environment
2. Install dependencies:
   - `pip install -r dc_los_django/requirements.txt`
3. Configure environment variables in `.env` or `.local-env`
4. Run migrations:
   - `python manage.py migrate`
5. Run tests:
   - `pytest`
6. Format code:
   - `black --check .`

#### Frontend
1. Install dependencies:
   - `npm install`
2. Run development server:
   - `npm run serve`
3. Build for production:
   - `npm run build`
4. Lint:
   - `npm run lint`

### Key Points
- Strong separation of backend API and frontend UI
- Robust loan, compliance, and document workflow support
- Production deployment via GitHub Actions and AWS SSM
- Comprehensive documentation through MkDocs
- Standard developer tooling: formatting, linting, testing
- Integration-capable architecture for external services

If you want, I can also turn this into a clean project summary for a single-page documentation note.
