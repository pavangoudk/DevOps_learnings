# GitLab CI/CD: The Ultimate Master Guide

## 1. Core Concepts & Methodologies
*   **Continuous Integration (CI):** Automation to build and test code every time a developer pushes changes.
*   **Continuous Delivery (CD):** Automation that prepares a release-ready artifact. Deployment to production is a **manual decision** (click a button).
*   **Continuous Deployment (CD):** Fully automated process where code moves from commit to production **without human intervention**.

---

## 2. Infrastructure: Runners & Executors
### GitLab Runners (The Workers)
*   **Shared Runners:** Available to every project in the GitLab instance.
*   **Group Runners:** Available to all projects within a specific group.
*   **Specific Runners:** Locked to one project for specialized needs (e.g., Mac builds, sensitive data).

### Executors (The Environment)
*   **Docker:** (Most Common) Uses images to provide a clean, isolated environment for every job.
*   **Kubernetes:** Highly scalable; runs jobs as pods in a cluster.
*   **Shell:** Runs directly on the host machine. High performance, zero isolation.
*   **Docker Machine:** Used for autoscaling runners in the cloud (AWS/GCP).
*   **VirtualBox/Parallels:** Runs jobs in a full VM (useful for Windows/macOS testing).
*   **SSH:** Executes commands on a remote server.

---

## 3. The Configuration File: `.gitlab-ci.yml`
This file lives in your repository root and controls everything.

### Basic Linear Pipeline Example
Jobs run one stage at a time.
```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script: 
    - echo "Compiling..."
    - mkdir output && touch output/binary.exe
  artifacts:
    paths:
      - output/

test_job:
  stage: test
  script: echo "Testing..."

deploy_job:
  stage: deploy
  script: echo "Deploying..."
  environment: production
  when: manual  # This makes it Continuous Delivery
```

---

## 4. Advanced Pipeline Architectures

### Directed Acyclic Graph (DAG)
Uses `needs` to break the stage bottleneck.
```yaml
ios_build:
  stage: build
  script: echo "Building iOS"

ios_test:
  stage: test
  needs: ["ios_build"] # Starts immediately after ios_build finishes
  script: echo "Testing iOS"
```

### Parent-Child Pipeline
Triggers a sub-pipeline within the same project (Monorepos).
```yaml
trigger_app_pipeline:
  trigger:
    include: path/to/child-ci.yml
    strategy: depend
```

### Multi-Project Pipeline
Triggers a pipeline in a different repository.
```yaml
trigger_external_docs:
  trigger:
    project: group/documentation-project
```

---

## 5. Job Keywords & Data Handling
*   **`rules`:** Determines if a job runs (e.g., `if: $CI_COMMIT_BRANCH == "main"`).
*   **`tags`:** Directs a job to a specific runner (e.g., `tags: [gpu]`).
*   **`parallel`:** Runs multiple instances of a job simultaneously.
*   **`services`:** Link another Docker container (like `postgres`) to your job.
*   **Cache vs. Artifacts:**
    *   **Cache:** For dependencies (`node_modules`). Temporary, speeds up runs.
    *   **Artifacts:** For build results (`app.zip`). Guaranteed, passed between stages.

---

## 6. Integrated Security Scanning
Enable these by including GitLab's managed templates.

### Static Scanning (Source Code)
*   **SAST:** Scans for code vulnerabilities.
*   **Secret Detection:** Prevents leaking API keys/passwords.
*   **IaC Scanning:** Scans Terraform/Kubernetes configs.
*   **Dependency Scanning:** Checks for vulnerable libraries.
*   **License Scanning:** Compliance checks on third-party licenses.

### Dynamic Scanning (Running App)
*   **DAST:** Attacks the running web application to find flaws.
*   **API Security:** Scans REST/GraphQL endpoints.
*   **Container Scanning:** Checks Docker images for OS vulnerabilities.
*   **Fuzz Testing:** Sends random data to find edge-case crashes.

### Configuration Example for Security:
```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml
  - template: Jobs/Secret-Detection.gitlab-ci.yml
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml
```
