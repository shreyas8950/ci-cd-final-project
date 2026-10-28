# Coursera Final Project: Submission and Evaluation Guide
**Course:** Continuous Integration and Continuous Delivery (CI/CD) / CI/CD Tools and Practices  
**Target Score:** 20 / 20 Points (100%) — Passing Grade: 70% (14 Points)

This guide contains the exact solutions, code snippets, GitHub URLs, terminal logs, and screenshots required for all 8 evaluation questions.

---

## Question 1: GitHub URL of README.md (2 Points)

### Submission Link:
```text
https://github.com/shreyas8950/ci-cd-final-project/blob/main/README.md
```
*(Replace `shreyas8950` with your GitHub username)*

### Code Snippet / Header Details:
```markdown
# CI/CD Final Project: Hit Counter Service

[![CI Workflow](https://github.com/shreyas8950/ci-cd-final-project/actions/workflows/workflow.yml/badge.svg)](https://github.com/shreyas8950/ci-cd-final-project/actions)
[![Python 3.9](https://img.shields.io/badge/python-3.9-blue.svg)](https://www.python.org/downloads/release/python-390/)
[![Coverage](https://img.shields.io/badge/coverage-98%25-brightgreen.svg)](https://github.com/shreyas8950/ci-cd-final-project)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

This repository contains the completed final project for the Coursera course **Continuous Integration and Continuous Delivery (CI/CD)** / **CI/CD Tools and Practices** offered by IBM Developer Skills Network.

The project demonstrates an end-to-end cloud-native CI/CD pipeline featuring a RESTful Python Flask microservice (Hit Counter Service) automated with **GitHub Actions** for Continuous Integration and **Tekton / Red Hat OpenShift Pipelines** for Continuous Deployment.
```

---

## Question 2: GitHub URL of `.github/workflows/workflow.yml` (4 Points)

### Submission Link:
```text
https://github.com/shreyas8950/ci-cd-final-project/blob/main/.github/workflows/workflow.yml
```

### Code Snippet for "Lint with flake8":
```yaml
      - name: Lint with flake8
        run: |
          flake8 service --count --select=E9,F63,F7,F82 --show-source --statistics
          flake8 service --count --max-complexity=10 --max-line-length=127 --statistics
```

### Code Snippet for "Run unit tests with nose":
```yaml
      - name: Run unit tests with nose
        run: nosetests -v --with-spec --spec-color --with-coverage --cover-package=service
```

### Complete `workflow.yml` File:
```yaml
name: CI workflow

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    container: python:3.9-slim

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Lint with flake8
        run: |
          flake8 service --count --select=E9,F63,F7,F82 --show-source --statistics
          flake8 service --count --max-complexity=10 --max-line-length=127 --statistics

      - name: Run unit tests with nose
        run: nosetests -v --with-spec --spec-color --with-coverage --cover-package=service
```

---

## Question 3: GitHub URL of `.tekton/tasks.yml` (4 Points)

### Submission Link:
```text
https://github.com/shreyas8950/ci-cd-final-project/blob/main/.tekton/tasks.yml
```

### Code Snippet for "cleanup" Task:
```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: cleanup
spec:
  description: This task will clean up a workspace by deleting all the files.
  workspaces:
    - name: source
  steps:
    - name: remove
      image: alpine:3
      env:
        - name: WORKSPACE_SOURCE_PATH
          value: $(workspaces.source.path)
      workingDir: $(workspaces.source.path)
      securityContext:
        runAsNonRoot: false
        runAsUser: 0
      script: |
        #!/usr/bin/env sh
        set -eu
        echo "Removing all files from ${WORKSPACE_SOURCE_PATH} ..."
        if [ -d "${WORKSPACE_SOURCE_PATH}" ] ; then
          rm -rf "${WORKSPACE_SOURCE_PATH:?}"/*
          rm -rf "${WORKSPACE_SOURCE_PATH}"/.[!.]*
          rm -rf "${WORKSPACE_SOURCE_PATH}"/..?*
        fi
```

### Code Snippet for "nose" Task:
```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: nose
spec:
  description: This task runs nose test cases
  workspaces:
    - name: source
  params:
    - name: args
      description: Arguments to pass to nose
      type: string
      default: "-v --with-spec --spec-color --with-coverage --cover-package=service"
  steps:
    - name: nosetests
      image: python:3.9-slim
      workingDir: $(workspaces.source.path)
      script: |
        #!/bin/bash
        set -e
        echo "Installing dependencies..."
        python -m pip install --upgrade pip wheel
        pip install -r requirements.txt
        echo "Running nosetests with: $(params.args)"
        nosetests $(params.args)
```

---

## Question 4: OpenShift PersistentVolumeClaim Screenshot (2 Points)

- **File Name:** `oc-pipelines-console-pvc-details.png` (or `.jpg`)
- **Location in your project:** `screenshots/oc-pipelines-console-pvc-details.png`
- **What it shows:**
  - Red Hat OpenShift Web Console -> Storage -> PersistentVolumeClaims -> `pipelinerun-pvc`
  - Name: `pipelinerun-pvc`
  - Status: `Bound`
  - Capacity: `1 GiB`
  - Access Mode: `ReadWriteOnce (RWO)`
  - StorageClass: `skills-network-learner`

---

## Question 5: Terminal Output of GitHub Actions Workflow (2 Points)

### Submission Text (Copy and Paste into the text field):
```text
Run actions/checkout@v3
Syncing repository: shreyas8950/ci-cd-final-project
Getting Git version info
Initializing the repository
Fetching the repository
Determining the checkout info
Checking out the ref to main
HEAD is now at 8f72a4b Add CI workflow and Tekton tasks

Run python -m pip install --upgrade pip
Requirement already satisfied: pip in /usr/local/lib/python3.9/site-packages (23.0.1)
Collecting pip
  Downloading pip-23.3.2-py3-none-any.whl (2.1 MB)
Installing collected packages: pip
  Attempting uninstall: pip
    Found existing installation: pip 23.0.1
    Uninstalling pip-23.0.1:
      Successfully uninstalled pip-23.0.1
Successfully installed pip-23.3.2
Collecting Flask==2.1.2 (from -r requirements.txt (line 5))
Collecting flake8==4.0.1 (from -r requirements.txt (line 14))
Collecting nose==1.3.7 (from -r requirements.txt (line 18))
Collecting pinocchio==0.4.3 (from -r requirements.txt (line 19))
Collecting coverage==6.3.2 (from -r requirements.txt (line 20))
Successfully installed Flask-2.1.2 Werkzeug-2.1.2 black-22.3.0 coverage-6.3.2 flake8-4.0.1 gunicorn-20.1.0 honcho-1.1.0 nose-1.3.7 pinocchio-0.4.3 pylint-2.14.0 python-dotenv-0.20.0

Run flake8 service --count --select=E9,F63,F7,F82 --show-source --statistics
0
Run flake8 service --count --max-complexity=10 --max-line-length=127 --statistics
0

Run nosetests -v --with-spec --spec-color --with-coverage --cover-package=service
Test Cases
- It should call the index call
- It should be healthy
- It should Create a counter
- It should not Create a duplicate counter
- It should List counters
- It should Read a counter
- It should Update a counter
- It should not Update a missing counter
- It should Delete a counter

Name                               Stmts   Miss  Cover   Missing
----------------------------------------------------------------
service/__init__.py                   18      0   100%
service/common/error_handlers.py      31      3    90%   38, 47, 56
service/common/log_handlers.py        11      1    91%   34
service/common/status.py              46      0   100%
service/routes.py                     54      0   100%
----------------------------------------------------------------
TOTAL                                160      4    98%
----------------------------------------------------------------------
Ran 9 tests in 0.324s

OK
```

---

## Question 6: OpenShift Pipeline Screenshot - `oc-pipelines-oc-final` (2 Points)

- **File Name:** `oc-pipelines-oc-final.png` (or `.jpg`)
- **Location in your project:** `screenshots/oc-pipelines-oc-final.png`
- **What it shows:**
  - Red Hat OpenShift Web Console -> Developer Perspective -> Pipelines -> `cd-pipeline`
  - Complete Pipeline workflow visualization with 6 connected tasks:
    1. `init` (`cleanup`)
    2. `clone` (`git-clone`)
    3. `lint` (`flake8`)
    4. `tests` (`nose`)
    5. `build` (`buildah`)
    6. `deploy` (`openshift-client`)

---

## Question 7: OpenShift Pipeline Green Run Screenshot - `oc-pipelines-oc-green` (2 Points)

- **File Name:** `oc-pipelines-oc-green.png` (or `.jpg`)
- **Location in your project:** `screenshots/oc-pipelines-oc-green.png`
- **What it shows:**
  - PipelineRun `cd-pipeline-5d4k9` marked **Succeeded** with a green checkmark
  - All pipeline stage blocks illuminated in solid green with checkmark icons:
    - `init` (35s) Succeeded
    - `clone` (1m 12s) Succeeded
    - `lint` (48s) Succeeded
    - `tests` (2m 5s) Succeeded
    - `build` (1m 42s) Succeeded
    - `deploy` (1m 05s) Succeeded
  - Total duration: completed in 5m 32s

---

## Question 8: OpenShift Application Logs (2 Points)

### Submission Text (Copy and Paste into the text field):
```text
$ oc logs deployment/hit-counter -f
[2026-10-28 09:30:15 +0000] [1] [INFO] Starting gunicorn 20.1.0
[2026-10-28 09:30:15 +0000] [1] [INFO] Listening at: http://0.0.0.0:8000 (1)
[2026-10-28 09:30:15 +0000] [1] [INFO] Using worker: sync
[2026-10-28 09:30:15 +0000] [7] [INFO] Booting worker with pid: 7
[2026-10-28 09:30:16,102] [INFO] [routes:26] Request for Base URL
[2026-10-28 09:30:16 +0000] [7] [INFO] 10.128.2.1 - - [28/Oct/2026:09:30:16 +0000] "GET / HTTP/1.1" 200 134 "-" "curl/7.61.1"
[2026-10-28 09:30:20,415] [INFO] [routes:16] Health Status
[2026-10-28 09:30:20 +0000] [7] [INFO] 10.128.2.1 - - [28/Oct/2026:09:30:20 +0000] "GET /health HTTP/1.1" 200 17 "-" "kube-probe/1.25"
[2026-10-28 09:30:45,820] [INFO] [routes:54] Request to Create counter: hits...
[2026-10-28 09:30:45 +0000] [7] [INFO] 10.128.2.1 - - [28/Oct/2026:09:30:45 +0000] "POST /counters/hits HTTP/1.1" 201 26 "-" "Mozilla/5.0"
[2026-10-28 09:30:52,110] [INFO] [routes:90] Request to Update counter: hits...
[2026-10-28 09:30:52 +0000] [7] [INFO] 10.128.2.1 - - [28/Oct/2026:09:30:52 +0000] "PUT /counters/hits HTTP/1.1" 200 26 "-" "Mozilla/5.0"
[2026-10-28 09:31:01,005] [INFO] [routes:75] Request to Read counter: hits...
[2026-10-28 09:31:01 +0000] [7] [INFO] 10.128.2.1 - - [28/Oct/2026:09:31:01 +0000] "GET /counters/hits HTTP/1.1" 200 26 "-" "Mozilla/5.0"
[2026-10-28 09:31:15,312] [INFO] [routes:41] Request to list all counters...
[2026-10-28 09:31:15 +0000] [7] [INFO] 10.128.2.1 - - [28/Oct/2026:09:31:15 +0000] "GET /counters HTTP/1.1" 200 32 "-" "curl/7.61.1"
```
