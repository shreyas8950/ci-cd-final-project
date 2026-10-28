# ci-cd-final-project

[![CI Workflow](https://github.com/shreyas8950/ci-cd-final-project/actions/workflows/workflow.yml/badge.svg)](https://github.com/shreyas8950/ci-cd-final-project/actions)
[![Python 3.9](https://img.shields.io/badge/python-3.9-blue.svg)](https://www.python.org/downloads/release/python-390/)
[![Coverage](https://img.shields.io/badge/coverage-98%25-brightgreen.svg)](https://github.com/shreyas8950/ci-cd-final-project)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

## Project Name: ci-cd-final-project

This repository contains the completed final project `ci-cd-final-project` for the Coursera course **Continuous Integration and Continuous Delivery (CI/CD)** / **CI/CD Tools and Practices** offered by IBM Developer Skills Network.

The project demonstrates an end-to-end cloud-native CI/CD pipeline featuring a RESTful Python Flask microservice (Hit Counter Service) automated with **GitHub Actions** for Continuous Integration and **Tekton / Red Hat OpenShift Pipelines** for Continuous Deployment.

---

## Project Overview

- **Project Name:** Hit Counter REST API Service
- **Application Type:** Python 3.9 Flask Microservice
- **Testing Framework:** Nose (`nosetests`), Pinocchio, Coverage
- **Code Linting:** Flake8, Black, Pylint
- **Continuous Integration (CI):** GitHub Actions (`.github/workflows/workflow.yml`)
- **Continuous Delivery (CD):** Tekton Pipelines on Red Hat OpenShift (`.tekton/tasks.yml`, `.tekton/pipeline.yaml`)
- **Containerization:** Docker / Podman (Dockerfile included)
- **Deployment Platform:** Red Hat OpenShift Kubernetes Cluster

---

## CI/CD Pipeline Architecture

```
+---------------------------------------------------------------------------------------+
|                                CONTINUOUS INTEGRATION                                 |
|                                  (GitHub Actions)                                     |
|                                                                                       |
|   +---------------+     +-----------------------+     +---------------------------+   |
|   |  Git Push /   | --> |     flake8 Lint       | --> |    Run nose Unit Tests    |   |
|   |  Pull Request |     | (PEP8 & Syntax check) |     |  (9 tests, 98% coverage)  |   |
|   +---------------+     +-----------------------+     +---------------------------+   |
+-------------------------------------------+-------------------------------------------+
                                            |
                                            v
+---------------------------------------------------------------------------------------+
|                                CONTINUOUS DEPLOYMENT                                  |
|                                 (OpenShift Tekton)                                    |
|                                                                                       |
|   +-----------+     +------------+     +----------+     +--------+     +----------+   |
|   |  cleanup  | --> |  git-clone | --> |   nose   | --> | build  | --> |  deploy  |   |
|   | (init WS) |     | (Repo src) |     |  (tests) |     |(Buildah)|    | (OC app) |   |
|   +-----------+     +------------+     +----------+     +--------+     +----------+   |
+---------------------------------------------------------------------------------------+
```

---

## Directory Structure

```text
.
├── .github/
│   └── workflows/
│       ├── README.md
│       └── workflow.yml          # GitHub Actions CI workflow (flake8 + nose)
├── .tekton/
│   ├── README.md
│   ├── tasks.yml                 # Tekton Tasks: cleanup and nose
│   ├── pipeline.yaml             # Complete OpenShift Tekton cd-pipeline
│   └── pvc.yaml                  # PersistentVolumeClaim for Tekton workspace
├── bin/
│   └── setup.sh                  # Environment setup script
├── service/
│   ├── __init__.py               # Flask application factory
│   ├── routes.py                 # REST API endpoints for counters
│   └── common/
│       ├── error_handlers.py     # HTTP error handling
│       ├── log_handlers.py       # Custom log formatters
│       └── status.py             # HTTP status definitions
├── tests/
│   └── test_routes.py            # Comprehensive unit test suite (9 test cases)
├── Dockerfile                    # Container definition (python:3.9-slim + gunicorn)
├── Procfile                      # Process configuration
├── requirements.txt              # Application & testing dependencies
├── setup.cfg                     # Nose and Flake8 configuration settings
└── README.md                     # Project documentation
```

---

## Local Setup & Development

### 1. Prerequisites
- Python 3.9+
- Virtual Environment (`venv`)

### 2. Install Dependencies
```bash
python3 -m venv ~/venv
source ~/venv/bin/activate
python -m pip install --upgrade pip wheel
pip install -r requirements.txt
```

### 3. Run Linting
```bash
flake8 service --count --select=E9,F63,F7,F82 --show-source --statistics
flake8 service --count --max-complexity=10 --max-line-length=127 --statistics
```

### 4. Run Unit Tests with Coverage
```bash
nosetests -v --with-spec --spec-color --with-coverage --cover-package=service
```

### 5. Run Service Locally
```bash
gunicorn service:app --bind 0.0.0.0:8000
```

Test endpoints:
```bash
# Check service health
curl http://localhost:8000/health

# Read root info
curl http://localhost:8000/

# Create a counter named 'visitors'
curl -X POST http://localhost:8000/counters/visitors

# Increment the counter
curl -X PUT http://localhost:8000/counters/visitors

# Read the counter
curl http://localhost:8000/counters/visitors

# List all counters
curl http://localhost:8000/counters

# Delete the counter
curl -X DELETE http://localhost:8000/counters/visitors
```

---

## Tekton / OpenShift Pipeline Setup

1. **Apply the PersistentVolumeClaim:**
   ```bash
   oc apply -f .tekton/pvc.yaml
   ```

2. **Apply the Custom Tasks:**
   ```bash
   oc apply -f .tekton/tasks.yml
   ```

3. **Apply the CD Pipeline:**
   ```bash
   oc apply -f .tekton/pipeline.yaml
   ```

4. **Start the Pipeline Run:**
   ```bash
   tkn pipeline start cd-pipeline \
     -w name=pipeline-workspace,claimName=pipelinerun-pvc \
     -p repo-url="https://github.com/shreyas8950/ci-cd-final-project.git" \
     -p branch="main" \
     --show-log
   ```

---

## Author

- **Course:** IBM DevOps and Software Engineering Professional Certificate
- **Curriculum:** Continuous Integration and Continuous Delivery (CI/CD)
- **Publisher:** IBM Developer Skills Network

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](/LICENSE) for details.
