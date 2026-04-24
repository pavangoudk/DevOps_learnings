# GitLab Organization Structure (Git Associate Exam)

This hierarchy is essential for organizing work and managing permissions within GitLab.

### 1. The Hierarchy
*   **Groups:** The top-level container used to organize projects and people. You manage members and permissions here, which inherit down to all subgroups and projects.
*   **Subgroups:** Groups inside groups. They allow for a nested folder-like structure (e.g., `Company > Department > Team`).
*   **Projects:** The fundamental unit where the actual work happens. It contains the **Repository**, CI/CD settings, and Issues.

### 2. Planning and Tracking
*   **Epics:** High-level goals or themes that span multiple issues and projects. They help track progress over long periods. *(Note: Only available in Groups, not Projects).*
*   **Issues:** The primary tool for tracking individual pieces of work, bugs, or features. 
*   **Participants:** Any user involved in an issue or Merge Request (by being assigned, mentioned, or commenting).

### 3. Management Tools
*   **Labels:** Tags used to categorize issues and Merge Requests. They can be **Project-level** or **Group-level** (inherited).
*   **Milestones:** Used to track work within a specific period (e.g., `Sprint 1`, `Release v1.0`). They help group issues/merge requests by a target date.
*   **Boards:** A visual management tool (Kanban/Scrum style) used to track issues through different stages (e.g., `To Do`, `Doing`, `Done`) based on Labels.

---

### Summary Table for Exam Preparation


| Feature | Level | Purpose |
| :--- | :--- | :--- |
| **Epics** | Group | Strategic goals / Multi-issue tracking |
| **Milestones** | Group/Project | Time-boxed release or iteration tracking |
| **Labels** | Group/Project | Categorization and Board workflow |
| **Boards** | Group/Project | Visual workflow management |
| **Subgroups** | Group | Nested organizational structure |

### Sample YAML: Label-Based Automation
While labels are UI-based, you can use them in `.gitlab-ci.yml` for logic:

```yaml
deploy_prod:
  stage: deploy
  script:
    - echo "Deploying..."
  rules:
    # Example: Only run if the Merge Request has a specific label
    - if: '\$CI_MERGE_REQUEST_LABELS =~ /ready-for-prod/'
```

# GitLab Roles and Permissions (Exam Essentials)

GitLab uses a hierarchical permission system. Permissions are inherited: if you have a role in a **Group**, you automatically have that same role in all its **Subgroups** and **Projects**.

### 1. The Five Main Roles

*   **Guest:** The lowest level. Can see the project but cannot see code. Useful for external stakeholders.
    *   *Key action:* Can create issues and post comments.
*   **Reporter:** "The Observer." Ideal for QA or project managers who don't write code.
    *   *Key action:* Can view code, pull/clone the repository, and manage issues/labels.
*   **Developer:** "The Contributor." The standard role for someone writing code daily.
    *   *Key action:* Can create branches, push code, and create Merge Requests. **Cannot** push to protected branches by default.
*   **Maintainer:** "The Project Lead." Responsible for the health of the project.
    *   *Key action:* Can push to protected branches, edit project settings, and manage runners/deploy keys.
*   **Owner:** "The Administrator." Only available at the **Group** level (or for the creator of a personal project).
    *   *Key action:* Can delete the project, change visibility, and manage group-level settings (like billing).

---

### 2. Comparison Matrix


| Feature | Guest | Reporter | Developer | Maintainer | Owner |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **View Project Content** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Download/Clone Code** | ❌ | ✅ | ✅ | ✅ | ✅ |
| **Create Issues** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Manage Issues (Labels/Assign)** | ❌ | ✅ | ✅ | ✅ | ✅ |
| **Push to Non-Protected Branch** | ❌ | ❌ | ✅ | ✅ | ✅ |
| **Manage Merge Requests** | ❌ | ✅ | ✅ | ✅ | ✅ |
| **Merge to Protected Branch** | ❌ | ❌ | ❌* | ✅ | ✅ |
| **Delete Project** | ❌ | ❌ | ❌ | ❌ | ✅ |

*\*Developers can be granted merge rights on specific protected branches, but they don't have it by default.*

### 3. Special Permissions
*   **Auditor:** A special read-only role (available in self-managed Premium/Ultimate) for compliance.
*   **External Users:** Can be flagged so they only see projects they are explicitly invited to, even if the instance has internal projects.

Would you like to review **Protected Branches** and how they interact with the **Maintainer** role for the exam?


# Protected Branches & Maintainer Permissions

In GitLab, **Protected Branches** are a security feature used to prevent accidental or malicious changes to critical branches (like `main` or `production`).

### 1. Default Protections
When a branch is protected, GitLab enforces the following by default:
*   **Prevent Force Pushes:** No one can overwrite the branch history.
*   **Prevent Deletion:** The branch cannot be deleted.
*   **Code Review Enforcement:** Changes must go through a **Merge Request (MR)**.

### 2. Interaction with Roles
The **Maintainer** role is the primary gatekeeper for protected branches.

*   **Developers:** By default, they **cannot** push directly to or merge into a protected branch. They must create a feature branch and submit an MR.
*   **Maintainers:** By default, they are the only role allowed to **Accept/Merge** MRs into a protected branch and push changes directly.

### 3. Customizing Access
In the Project Settings (`Settings > Repository > Protected branches`), a **Maintainer** can refine these permissions:

*   **Allowed to merge:** Choose which roles (or specific users) can merge MRs.
*   **Allowed to push:** Choose who can push directly (usually restricted to Maintainers or "No one").
*   **Allow force push:** Can be toggled on if absolutely necessary (not recommended).

---

### Sample YAML: Branch-Specific Jobs
You can use the `$CI_COMMIT_REF_NAME` variable or `rules` to ensure certain jobs only run on protected branches.

```yaml
deploy_to_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
  rules:
    # Only runs if the branch is exactly 'main'
    - if: \$CI_COMMIT_BRANCH == "main"
      when: manual  # Adds a button in the UI for a Maintainer to click
```

### Key Exam Tip:
If a user with the **Developer** role tries to push directly to `main` and gets a `pre-receive hook declined` error, the most likely reason is that the branch is **Protected**.

# Git and GitLab Terminology

These are the fundamental building blocks of version control that you'll need to know for the **Git Associate Exam**.

### 1. The Basics
*   **Commit:** A "snapshot" of your changes at a specific point in time. Each commit has a unique ID (SHA) and a message describing what was changed.
*   **Push:** The act of sending your local commits to the remote GitLab server so others can see and use them.

### 2. Navigation and Structure
*   **Branch:** A parallel version of your repository. It allows you to work on a feature or bug fix (e.g., `feature-login`) without affecting the stable `main` branch code.
*   **Tag:** A permanent "bookmark" for a specific commit, usually used to mark version releases (e.g., `v1.0.1`). Unlike branches, tags do not move when new commits are added.
*   **Checkout:** The process of switching between different branches or moving back to a specific commit. It updates the files in your local workspace to match that version.

### 3. Integration
*   **Merge:** The process of combining the changes from one branch (e.g., `feature-branch`) into another (e.g., `main`).
*   **Workspace (Working Directory):** The actual folder on your computer where you see and edit your files. It represents one specific state of the repository that you currently have "checked out."

---

### Comparison: Branch vs. Tag


| Feature | **Branch** | **Tag** |
| :--- | :--- | :--- |
| **Purpose** | Active development | Versioning/Release |
| **Movement** | Moves forward with new commits | Stays fixed to one commit |
| **Deletion** | Deleted after merging | Usually kept forever |

### Sample YAML: Running jobs on Tags
You can trigger specific CI/CD jobs only when a **Tag** is created (perfect for automated releases).

```yaml
release_job:
  stage: deploy
  script:
    - echo "Building the release version..."
  rules:
    - if: $CI_COMMIT_TAG  # This job only exists if a Tag is pushed
```

Would you like to see the **Git commands** (CLI) associated with these terms, such as `git branch` or `git commit -m`?


# Git CLI & GitLab Fundamentals

### 1. Essential Git Commands
These commands manage the lifecycle of your code from your local **Workspace** to the GitLab server.

*   **Initialize/Clone:**
    *   `git init`: Create a new local repo.
    *   `git clone <url>`: Download an existing project.
*   **Stage & Commit:**
    *   `git add <file>`: Move changes to the staging area.
    *   `git commit -m "message"`: Save your changes locally.
*   **Synchronize:**
    *   `git push origin <branch>`: Send local commits to GitLab.
    *   `git pull origin <branch>`: Fetch and merge remote changes to your local machine.
*   **Branching:**
    *   `git checkout -b <name>`: Create and switch to a new branch.

### 2. CI/CD & The .gitlab-ci.yml File
The `.gitlab-ci.yml` file is the heart of GitLab automation. It defines **Stages** and **Jobs**.

```yaml
# Simple .gitlab-ci.yml example
stages:
  - build
  - test

compile:
  stage: build
  script:
    - echo "Compiling..."
    - mkdir build
    - touch build/app.exe
  artifacts:
    paths:
      - build/

unit_test:
  stage: test
  script:
    - echo "Testing..."
    - test -f build/app.exe
```

### 3. GitLab Runner
The **Runner** is the application that actually executes the scripts defined in your YAML file.
*   It polls GitLab for "jobs" to run.
*   It uses **Executors** (Docker, Shell, etc.) to create the environment.
*   It sends logs and results back to the GitLab UI.

### 4. SSH Keys
SSH keys provide a secure way to log into GitLab from your terminal without typing your password every time.

*   **Generation:** `ssh-keygen -t ed25519 -C "email@example.com"` creates a private and public key pair.
*   **Usage:** You copy the **Public Key** (`id_ed25519.pub`) and paste it into **GitLab User Settings > SSH Keys**.
*   **Security:** The **Private Key** stays on your computer and should never be shared.

---

### Summary Checklist for Exam

| Concept | Key Point |
| :--- | :--- |
| **Git Command** | `git push` sends code to the remote. |
| **.gitlab-ci.yml** | Must be in the root directory. |
| **Runner** | Needs a **Token** and **URL** to register. |
| **SSH Key** | Use the **Public** key for GitLab settings. |

# GitLab Collaboration & Development Tools

Beyond standard repositories, GitLab provides integrated tools to streamline code review, documentation, and environment setup.

### 1. Snippets
Snippets are blocks of code or text that you can share with others. They are useful for storing small scripts, configuration examples, or notes that don't belong in a full project repository.
*   **Personal Snippets:** Owned by a user and can be private, internal, or public.
*   **Project Snippets:** Specific to a project and available to project members.

### 2. Wikis
Every GitLab project can host a Wiki, which is a separate system for documentation based on Markdown.
*   **Purpose:** Ideal for high-level documentation, setup guides, and project roadmaps that are too extensive for a `README.md`.
*   **Storage:** Technically, a Wiki is a separate Git repository, meaning you can clone it and edit it locally.

### 3. Web IDE
The Web IDE is a powerful, browser-based editor that allows you to make complex changes to multiple files without cloning the project locally.
*   **Code Review:** You can use it to view Merge Request changes side-by-side.
*   **Commit Flows:** It allows you to stage changes, write commit messages, and create new branches directly from the browser.

### 4. Docker (Integrated with GitLab)
GitLab has a deep integration with Docker to manage containerized workflows.
*   **Container Registry:** Every project has a built-in registry to store and manage Docker images created during CI/CD.
*   **Dev Containers:** GitLab supports remote development environments, allowing you to open the Web IDE in a pre-configured Docker container (via the "Remote Development" feature).

---

### Comparison: When to Use Which?


| Tool | Use Case |
| :--- | :--- |
| **Snippet** | Sharing a 10-line SQL query or a helper script. |
| **Wiki** | Writing a long user manual for your application. |
| **Web IDE** | Fixing a typo or small bug directly in the browser. |
| **Docker** | Creating a consistent environment for tests to run in. |

Would you like to know how to **link a Snippet** directly into an **Issue** or **Merge Request**?



















# GitLab Secure Stage: Security Testing

The **Secure Stage** integrates security directly into the DevOps lifecycle, moving security "left" so vulnerabilities are caught during development.

### 1. Static & Analysis Tools
*   **SAST:** Scans source code for vulnerabilities (like SQLi or XSS) without executing the code.
*   **Secret Detection:** Scans the repo for leaked credentials (API keys, tokens).
*   **Dependency Scanning:** Checks your `package.json`, `requirements.txt`, etc., for known vulnerabilities in third-party libraries.
*   **IaC Scanning:** Scans infrastructure files (Terraform, K8s) for misconfigurations.

### 2. Dynamic & Behavioral Tools
*   **DAST:** Attacks your *running* application to find flaws that only appear in a live environment.
*   **Container Scanning:** Scans the OS layers of your Docker images for vulnerabilities.
*   **License Compliance:** Scans dependencies to ensure they align with your legal policies (e.g., blocking "GPL").

### 3. Fuzz Testing: Web API vs. Coverage
*   **Web API Fuzzing:** Sends malformed or unexpected data to **API endpoints** (REST, GraphQL) to find crashes or logic flaws.
*   **Coverage-guided Fuzzing:** Takes a **library/function** and sends random inputs, using code coverage to "evolve" the inputs to reach deeper code paths.

### 4. Management & Reporting
*   **Vulnerability Reporting:** A per-project list of all detected flaws, allowing developers to dismiss, create issues, or resolve them.
*   **Security Dashboard/Center:** A high-level view (at the Group or Instance level) showing security trends and grades across multiple projects.
*   **Compliance Report:** Tracks compliance adherence, showing which projects have specific security features enabled.

---

### Sample YAML: Full Security Suite
You can enable most scanners simply by including the managed templates.

```yaml
stages:
  - build
  - test
  - deploy

include:
  - template: Jobs/SAST.gitlab-ci.yml
  - template: Jobs/Secret-Detection.gitlab-ci.yml
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml
  - template: Jobs/Container-Scanning.gitlab-ci.yml
  - template: Jobs/DAST.gitlab-ci.yml

# DAST requires a target URL to scan
dast:
  variables:
    DAST_WEBSITE: "https://my-app.com"
```

### Sample YAML: Overriding SAST
If you need to change a specific scanner's behavior:

```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml

sast:
  stage: security_check  # Move to a custom stage
  variables:
    SAST_EXCLUDED_PATHS: "spec, test, tests, tmp"
```

