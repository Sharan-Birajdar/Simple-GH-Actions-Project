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

dc_los_django

Backend Django project
apps/
 contains Django apps like 
company
, 
core
, 
loan
, etc.
dc_los_django contains Django settings, 
wsgi.py
, 
celery.py
, auth, logging, and app entrypoints
templates/
, 
static/
, 
documents/
, 
logs/
, 
scripts/
 support the backend
.github/workflows/
 contains CI/CD workflows for backend deployment and checks
requirements.txt
, 
pytest.ini
, setup.md, AGENTS.md, CLAUDE.md, 
mkdocs.yml
 are documentation and setup/config files
manage.py
 is the Django CLI entrypoint
reverse-pilot-frontend

Vue.js frontend application
package.json
 + package-lock.json
src/
 for Vue source files
public/
 static web assets
.github/workflows/
 contains frontend deployment workflows
README.md includes npm setup/build commands
Other supporting top-level folders include:

docs/
 for documentation and architecture notes
scripts/
 for project utilities
static/
 and 
templates/
 for shared web/static content

### Documentation
The backend includes an MkDocs documentation site with content for:
- Getting Started
- System Overview
- User Guides
- Operations
- Administration
- Compliance
- Integrations
- Calculations
- Technical Documentation
- Support
- Releases

Key docs are configured in mkdocs.yml.

### CI/CD Workflow
GitHub Actions workflows exist under workflows.

#### dev_code_check.yaml
- Triggered on PRs to `dev`
- Uses Ubuntu runner with PostgreSQL service
- Installs Python dependencies
- Creates a CI `.env`
- Runs Django migrations
- Executes `pytest`
- Checks formatting with `black`
- Validates DRF permission classes

#### dev_deploy.yml
- Triggered when PRs to `dev` are closed
- Uses AWS OIDC to assume deployment role
- Deploys via AWS SSM to EC2
- Installs system dependencies needed for PDF processing
- Syncs repo with `origin/dev`
- Activates virtual environment
- Installs Python requirements
- Runs Django system checks
- Applies database migrations safely

#### Other workflow files
- `beta_deploy.yml`
- `prod_deploy.yml`
- `docs_deploy.yml`
- `sonarqube.yml`
- `claude.yml`
- `claude-code-review.yml`

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
