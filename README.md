# CI/CD Overview

**CI/CD** is a method for frequently delivering apps to customers by introducing automation into the stages of software development.

### Core Concepts
*   **Continuous Integration (CI):** Developers merge their code changes into a shared repository as often as possible. Each merge triggers an automated build and test process to catch bugs early.
*   **Continuous Delivery (CD):** An extension of CI that automatically prepares code changes for a release to production. While the release is automated, a **manual human approval** is required to actually deploy the code.
*   **Continuous Deployment (CD):** The next step after delivery where every change that passes all stages of the production pipeline is released to customers **automatically**, with no human intervention.

### Implementation in GitLab
*   **.gitlab-ci.yml file:** A YAML-based configuration file located in the root of your repository. It serves as the blueprint for your pipeline, defining the **stages** (e.g., build, test, deploy), **jobs** (specific tasks), and the **scripts** the runners should execute.
*   **GitLab Runners:** Lightweight agents that execute the jobs defined in the `.gitlab-ci.yml` file. They pull the code, run the scripts, and report the results back to GitLab in real-time.

### Types of Runners
Runners are categorized by who has access to use them:
*   **Shared Runners:** Available to **all projects and groups** in a GitLab instance. They are ideal for jobs with similar requirements across multiple projects to avoid resource idling.
*   **Group Runners:** Dedicated to a **specific group** and its subgroups. All projects within that group can use these runners, which is useful for sharing resources within a single department or team.
*   **Specific (Project) Runners:** Associated with **individual projects**. Use these for jobs with special requirements, such as those needing specific security credentials or high-demand CI activity that shouldn't be slowed down by other projects.



# Defining Runners in .gitlab-ci.yml

Runners are not "created" inside the YAML file; they are registered to your GitLab instance/project separately. However, you **select** which runner executes a specific job using the `tags` keyword.

### 1. Selecting Runners with Tags
To ensure a job runs on a specific runner (e.g., a **Specific Runner** with specialized hardware), you match the tags defined during the runner's registration.

```yaml
stages:
  - build
  - test

# This job will only run on a runner tagged 'docker'
standard_job:
  stage: build
  tags:
    - docker
  script:
    - echo "Running on a generic Docker runner"

# This job will only run on a runner tagged 'gpu' or 'high-perf'
# Useful for Specific Runners used for heavy computation
heavy_job:
  stage: test
  tags:
    - gpu
    - high-perf
  script:
    - echo "Running on a specialized high-performance runner"
```

### 2. Using Shared Runners (Default)
If you do not specify any `tags`, GitLab will attempt to run the job on any available **Shared Runner** that is configured to run untagged jobs.

```yaml
# Runs on any available Shared Runner that accepts untagged jobs
untagged_job:
  stage: build
  script:
    - echo "I am running on whatever Shared Runner is free"
```

### 3. Defining Runner Images
While the runner is the host, you often define the environment (Docker image) it should use inside the YAML file.

```yaml
default:
  image: ruby:3.0  # Default image for all jobs if not specified

job_with_custom_image:
  image: python:3.9 # Overrides default for this specific job
  script:
    - python --version
```


# GitLab Pipelines

### Core Pipeline Structures
*   **Pipelines:** The top-level component of continuous integration, delivery, and deployment. They consist of **jobs**, which execute scripts, and **stages**, which define when those jobs run.
*   **Basic Pipeline:** The simplest configuration where jobs are grouped into stages (e.g., Build, Test, Deploy). All jobs in one stage must complete successfully before the next stage begins.
*   **Directed Acyclic Graphs (DAG) Pipeline:** Uses the `needs` keyword to allow jobs to run out of order. Instead of waiting for an entire stage to finish, a job starts as soon as its specific dependencies are met, speeding up the execution.

### Advanced Pipeline Architectures
*   **Parent-Child Pipelines:** A setup where a main pipeline (parent) triggers one or more sub-pipelines (children) within the same project. This keeps the `.gitlab-ci.yml` file clean and manageable by splitting configuration into multiple files.
*   **Child Pipelines:** Independent pipelines triggered by a parent. They run their own stages and jobs, and the parent can be configured to wait for the child’s completion or continue independently.
*   **Multi-Project Pipelines:** Pipelines that trigger workflows in **different projects**. This is essential for microservices architectures where a change in a library project needs to trigger a build or test in a dependent application project.

### Visualization
*   **GitLab Pipeline Graph:** A visual representation of the jobs and stages in a pipeline. It shows the status of each job (running, passed, failed), the dependencies between them, and provides a clear map of the entire automation workflow.


# GitLab Pipeline Examples

### 1. Basic Pipeline
Jobs run sequentially by stage. All jobs in `build` must finish before `test` starts.

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Compiling code..."

test_job:
  stage: test
  script:
    - echo "Running unit tests..."

deploy_job:
  stage: deploy
  script:
    - echo "Deploying to staging..."
```

### 2. Directed Acyclic Graph (DAG) Pipeline
The `test_job` starts immediately after `build_job` finishes, even if other jobs in the build stage are still running.

```yaml
stages:
  - build
  - test

build_a:
  stage: build
  script: echo "Building A..."

build_b:
  stage: build
  script: echo "Building B..."

test_a:
  stage: test
  needs: ["build_a"]
  script: echo "Testing A immediately after build_a finishes..."
```

### 3. Parent-Child Pipeline
The main file triggers a sub-pipeline defined in a separate local file.

**Main Pipeline (`.gitlab-ci.yml`):**
```yaml
stages:
  - triggers

trigger_child_pipeline:
  stage: triggers
  trigger:
    include: path/to/child-pipeline-config.yml
    strategy: depend
```

**Child Pipeline (`path/to/child-pipeline-config.yml`):**
```yaml
child_job:
  script:
    - echo "I am a job inside the child pipeline"
```

### 4. Multi-Project Pipeline
Triggers a pipeline in a completely different GitLab repository.

```yaml
stages:
  - deploy

trigger_other_project:
  stage: deploy
  trigger:
    project: my-group/another-project
    branch: main
```
# GitLab CI/CD: Execution & Management

### 1. Environments
Environments allow you to track deployments. You can see what version of your code is deployed to "Production," "Staging," or "Review" directly in GitLab.

```yaml
deploy_to_production:
  stage: deploy
  script:
    - echo "Deploying to production server..."
  environment:
    name: production
    url: https://myapp.com
```

### 2. Rules
`rules` determine whether a job is added to the pipeline. They replace the older `only/except` syntax and allow for complex logic based on variables, file changes, or branch names.

```yaml
test_job:
  script:
    - echo "Running tests..."
  rules:
    - if: \$CI_PIPELINE_SOURCE == "merge_request_event"  # Run only on Merge Requests
    - if: \$CI_COMMIT_BRANCH == "main"                  # Run on the main branch
```

### 3. Cache vs. Artifacts
While both store files, they serve very different purposes:

*   **Cache:** Used for project dependencies (like `node_modules` or `pip` packages) to speed up subsequent runs. It is **not** guaranteed to be available across different runners.
*   **Artifacts:** Used to pass intermediate build results (like a compiled `.exe` or a `dist/` folder) between stages. They are **guaranteed** to be available to the next stage and can be downloaded from the GitLab UI.

```yaml
build_job:
  stage: build
  cache:
    key: \${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 week
```

### 4. Tags
Tags are used to select specific **GitLab Runners**. If a runner has the tag `aws` and `linux`, and your job specifies those tags, that runner will pick up the job.

```yaml
compile_ios:
  stage: build
  tags:
    - macos
    - xcode-15
  script:
    - echo "This job only runs on a Mac runner with Xcode installed"
```


# GitLab CI/CD: Job Configuration and Execution

### 1. Jobs and Scripts
Jobs are the most fundamental element of a `.gitlab-ci.yml` file. The `script` keyword is the only mandatory part of a job; it contains the shell commands to be executed.

```yaml
job_name:
  script:
    - echo "Executing commands..."
    - ./run-my-build.sh
```

### 2. Order of Jobs and Severity
*   **Order:** By default, jobs in the same **stage** run in parallel. Stages run sequentially in the order defined in the `stages` block.
*   **Severity (Allow Failure):** You can control how a job failure affects the pipeline. Using `allow_failure: true` lets the pipeline continue even if that specific job fails.

```yaml
lint_job:
  stage: test
  script: - npm run lint
  allow_failure: true  # Pipeline won't stop if linting fails
```

### 3. Syntax for Grouping Jobs
You can group jobs visually in the pipeline graph by using a specific naming convention: `group_name:job_name`.

## 1. Visual Grouping (Naming Convention)
If you name jobs using a specific format—a name, a colon, and then a sub-name—GitLab collapses them into a single clickable bubble in the UI.

**Format:** `Group Name: Sub-job Name`

```yaml
stages:
  - test

# These three jobs will appear as one "Tests" bubble in the graph
"Tests: Unit":
  stage: test
  script: ./run-unit-tests.sh

"Tests: Integration":
  stage: test
  script: ./run-int-tests.sh

"Tests: E2E":
  stage: test
  script: ./run-e2e-tests.sh
```pt: echo "Part 2"
```

### 4. Services and Variables
*   **Services:** Used to run additional Docker containers (like MySQL or Redis) that the main job needs to interact with.
*   **Variables:** Define environment variables at the global level or per job.

```yaml
variables:
  DATABASE_URL: "postgres://user:pass@postgres:5432/db"

test_db:
  services:
    - postgres:14.3
  script:
    - psql -h postgres -d db -U user
```

### 5. Job Keywords: Dependencies vs. Needs
*   **Dependencies:** Used with **Artifacts**. It defines which previous jobs to download artifacts from. If not defined, all artifacts from previous stages are downloaded.
*   **Needs:** Used to create a **DAG (Directed Acyclic Graph)**. It allows a job to start as soon as its dependencies finish, ignoring stage sequencing.

```yaml
deploy_job:
  stage: deploy
  needs: ["build_job"] # Starts immediately after build_job, skipping test stage
  dependencies: ["build_job"] # Only downloads artifacts from build_job
```

### 6. Parallel and Trigger
*   **Parallel:** Runs multiple instances of the same job simultaneously, often used for sharding tests.
*   **Trigger:** Defines a job that starts a downstream pipeline (Child or Multi-project).

```yaml
# Parallel Example
sharded_tests:
  script: run_tests.sh
  parallel: 3 # Runs 3 instances of this job

# Trigger Example
deploy_sub_project:
  trigger: my-group/deployment-repo
```


# GitLab Runner Executors

Executors define the environment in which each job runs. Choosing the right one depends on your infrastructure and security needs.

### 1. Shell Executor (Common)
The simplest executor. It runs jobs directly on the host machine where the Runner is installed.
*   **Pros:** Easy to set up; no Docker required.
*   **Cons:** No isolation—jobs can mess with the host system; requires all dependencies to be pre-installed on the host.

### 2. Docker Executor (Most Common)
The standard choice for most CI/CD tasks. Every job runs inside a clean, isolated Docker container.
*   **Pros:** High isolation; highly customizable via images; easy to scale.
*   **Cons:** Requires Docker installation; slightly more overhead than Shell.

### 3. Kubernetes Executor (Common for Cloud)
Ideal for cloud-native setups. It automatically creates a new Pod for every CI/CD job in a Kubernetes cluster.
*   **Pros:** Highly scalable; great for organizations already using K8s; efficient resource management.
*   **Cons:** Complex configuration.

### 4. Docker Machine (Common for Auto-scaling)
Used for auto-scaling Docker executors. It creates "on-demand" virtual machines to run Docker containers and shuts them down when finished.
*   **Pros:** Cost-efficient (only pay for what you use).
*   **Cons:** Now in maintenance mode; largely replaced by Kubernetes or cloud-native scaling.

### 5. SSH Executor (Uncommon)
The Runner connects to a remote server via SSH to execute the job scripts.
*   **Pros:** Useful for deploying to legacy servers where you cannot install a Runner.
*   **Cons:** Weakest security; no environment isolation; slow performance.

### 6. VirtualBox / Parallels (Uncommon)
The Runner starts a full Virtual Machine (VM) for each job. 
*   **VirtualBox:** Cross-platform VM.
*   **Parallels:** Specifically for macOS environments.
*   **Pros:** Total isolation (even at the OS level).
*   **Cons:** Very slow to start; extremely resource-heavy compared to Docker.

---


| Executor | Isolation | Scaling | Recommended Use |
| :--- | :--- | :--- | :--- |
| **Docker** | High | Easy | Standard CI/CD |
| **Shell** | None | Hard | Simple scripts / local tools |
| **Kubernetes** | High | Excellent | Large-scale enterprise |
| **SSH** | Low | Manual | Legacy server deployments |


# GitLab Security Scanning

GitLab provides a comprehensive suite of security tools integrated directly into the CI/CD pipeline. Most of these can be enabled by including dedicated templates in your `.gitlab-ci.yml`.

### Static Analysis (Code & Configuration)
*   **SAST (Static Analysis Security Testing):** Scans your source code for known vulnerabilities (like SQL injection or Cross-Site Scripting) before the code is even compiled.
*   **Secret Detection:** Scans your repository to prevent sensitive information—like API keys, passwords, or certificates—from being accidentally committed.
*   **IaC (Infrastructure as Code) Scanning:** Checks your infrastructure configuration files (Terraform, Ansible, Kubernetes, CloudFormation) for security misconfigurations.
*   **Dependency Scanning:** Analyzes your project's external libraries and dependencies (e.g., via `npm`, `pip`, or `gem`) for known security flaws.
*   **License Scanning:** Scans your dependencies to ensure their licenses are compliant with your company’s policies (e.g., blocking GPL in a proprietary project).

### Dynamic & Behavioral Analysis
*   **DAST (Dynamic Analysis Security Testing):** Analyzes your running application (web apps or APIs) for vulnerabilities that only appear at runtime, such as authentication flaws.
*   **API Security:** Specifically tests your APIs (REST, GraphQL, SOAP) for vulnerabilities, logic flaws, and unauthorized access.
*   **Coverage-guided Fuzz Testing:** Sends unexpected, random, or malformed data inputs to your application to find bugs or crashes that standard tests might miss.

### Container & Environment Security
*   **Container Scanning:** Scans your Docker images for vulnerabilities in the OS packages and libraries installed within the container.
*   **Operational Container Scanning:** Continuously monitors containers running in your Kubernetes clusters to detect vulnerabilities that were discovered after the initial deployment.

---

### Implementation Example
To enable these, you typically add the following to your `.gitlab-ci.yml`:

```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml
  - template: Jobs/Secret-Detection.gitlab-ci.yml
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml
  - template: Jobs/Container-Scanning.gitlab-ci.yml
```






# GitLab Runner Registration & Executor Specification

The executor is specified during the runner registration process. You can do this either **interactively** (following prompts) or **non-interactively** (using a single command).

### 1. Interactive Registration
When you run the standard registration command, GitLab will prompt you for the executor type toward the end of the process.

**Command:**
```bash
sudo gitlab-runner register
```

**Prompts you will see:**
1.  **GitLab URL:** e.g., `https://gitlab.com`
2.  **Registration Token:** (From your Project/Group/Admin settings)
3.  **Description:** e.g., `my-docker-runner`
4.  **Tags:** e.g., `docker, linux`
5.  **Executor:** Type your choice here (e.g., `docker`, `shell`, `kubernetes`).
6.  **Default Image:** (If using Docker) e.g., `alpine:latest`.

---

### 2. Non-Interactive (One-Line) Registration
For automation or scripts, you can specify the executor using the `--executor` flag.

**Example for Docker:**
```bash
sudo gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --registration-token "YOUR_TOKEN" \
  --executor "docker" \
  --docker-image "alpine:latest" \
  --description "docker-runner" \
  --tag-list "docker,linux"
```

**Example for Shell:**
```bash
sudo gitlab-runner register \
  --non-interactive \
  --url "https://gitlab.com/" \
  --registration-token "YOUR_TOKEN" \
  --executor "shell" \
  --description "shell-runner" \
  --tag-list "shell,local"
```

---

### 3. Verification and Manual Editing
After registration, the executor and its settings are stored in the `config.toml` file. You can manually change the executor later by editing this file (though you must restart the runner for changes to take effect).

**File Location:**
*   **Linux:** `/etc/gitlab-runner/config.toml`
*   **Windows:** The directory where you installed the `gitlab-runner.exe`

```toml
[[runners]]
  name = "my-runner"
  url = "https://gitlab.com/"
  token = "TOKEN"
  executor = "docker"  # You can change this manually here
  [runners.docker]
    image = "alpine:latest"
```

### Summary of Executor String Names
When prompted or using the flag, use these exact strings:
*   `docker`
*   `shell`
*   `kubernetes`
*   `docker+machine` (for autoscaling)
*   `ssh`
*   `virtualbox`
*   `parallels`










