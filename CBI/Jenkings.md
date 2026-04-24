# Module: Key CI/CD and Jenkins Concepts

## 1. CI/CD Concepts
*   **Continuous Integration (CI)**: The practice of merging all developer working copies to a shared mainline several times a day. Includes automated building and testing.
*   **Continuous Delivery (CD)**: An extension of CI where code changes are automatically prepared for a release to production. Requires a manual "trigger" to deploy.
*   **Continuous Deployment**: Every change that passes all stages of your production pipeline is released to your customers automatically. No human intervention.
*   **Stages**: Source -> Build -> Test -> Staging -> Production.

## 2. Jenkins Jobs and Builds
*   **Jobs**: A runnable task controlled by Jenkins. Common types include **Freestyle** (simple, UI-based), **Pipeline** (as-code), and **Multibranch Pipeline**.
*   **Builds**: A specific execution of a job.
    *   **Triggers**: What starts the build (e.g., SCM poll, Webhook, Schedule).
    *   **Artifacts**: Files generated during a build (e.g., .jar, .war, .zip) that you want to preserve.
    *   **Repositories**: External storage for artifacts (e.g., Nexus, Artifactory).

## 3. Source Code Management (SCM)
*   **Infrastructure-as-Code (IaC)**: Managing and provisioning infrastructure through machine-readable definition files (like Ansible playbooks) rather than manual configuration.
*   **Incremental vs. Clean Checkout**: Incremental updates only pull changes (faster); Clean checkouts delete the workspace and pull everything fresh (more reliable).
*   **Branch/Merge**: Strategies like **GitFlow** or **Trunk-based Development** define how code is integrated.

## 4. Testing in Jenkins
*   **Unit Test**: Testing the smallest part of an application in isolation.
*   **Smoke Test**: Non-exhaustive testing to ensure the most important functions work.
*   **Acceptance Test**: Verification that the software meets business requirements.
*   **Functional Test**: Verifying the software against the functional requirements/specifications.

## 5. Distributed Builds (Master/Agent)
*   **Controller (Master)**: The central control plane. It schedules builds, dispatches tasks to agents, and monitors their state.
*   **Agents**: Small Java programs that run on remote machines to execute the actual build steps, allowing for parallel execution and platform-specific testing.

## 6. Plugins and API
*   **Plugins**: Add-ons that extend Jenkins functionality (e.g., Git, Docker, Slack integration). Managed via the **Plugin Manager**.
*   **REST API**: Used to interact with Jenkins programmatically. 
    *   **Why?**: To trigger builds, retrieve status, or create jobs from external scripts or dashboards.

## 7. Security and Auditing
*   **Authentication**: Verifying **who** the user is (LDAP, Github OAuth).
*   **Authorization**: Defining **what** the user can do (Matrix-based security, RBAC).
*   **Fingerprints**: MD5 checksums used to track versions of artifacts across different jobs and builds to maintain a "chain of custody."

## 8. Configuration Management
*   **Software Configuration Management (SCM)**: The process of tracking and controlling changes in software (using tools like Chef, Puppet, or Ansible).
*   **Importance**: Ensures environments are reproducible and prevents "it works on my machine" issues.


# Module: Advanced Jenkins Management and CI/CD Implementation

## 1. Advanced Job Management
*   **Job Organization**: Using **Folders** and **Views** to categorize jobs by project, team, or environment.
*   **Job Types**:
    *   **Freestyle**: Classic UI-based configuration.
    *   **Pipeline**: Modern "Pipeline-as-Code" (Jenkinsfile).
    *   **Matrix**: Running the same job with different configurations (e.g., testing on multiple OS or Java versions).
    *   **Maven**: Tailored for Java projects using Project Object Model (POM).
*   **Parameterized Jobs**: Allowing user input (strings, booleans, choices) at runtime to make jobs dynamic.

## 2. Builds and SCM Integration
*   **Triggers**: Methods to start builds, including **Polling SCM** (checking for changes), **Webhooks** (SCM pushes to Jenkins), and cron-like schedules.
*   **Build Steps**: Executing shell scripts (`sh`), Windows batch commands, or calling build tools like **Ant**, **Gradle**, or **Maven**.
*   **Hooks**: Automating the feedback loop where GitHub/Bitbucket notifies Jenkins instantly upon code check-in.

## 3. Testing and Code Quality
*   **Code Coverage**: Integrating tools like **JaCoCo** or **Cobertura** to measure how much code is tested.
*   **Test Reports**: Using the "Publish JUnit test result report" to visualize pass/fail trends and stack traces.
*   **Breaking Builds**: Configuring "thresholds" where a build fails automatically if test failures exceed a certain number or coverage drops.

## 4. Notifications and Alerts
*   **Communication**: Setting up **Email Extension**, Slack, or MS Teams notifications.
*   **Build Radiators**: High-visibility dashboards (Wallboards) used in offices to show real-time pipeline status.
*   **Alarming**: Configuring notifications to trigger only on specific events, such as "First Failure" or "Back to Normal."

## 5. Scaling with Distributed Builds
*   **Agent Types**:
    *   **SSH Agents**: Controller connects to Linux nodes via SSH.
    *   **Inbound (JNLP) Agents**: Agent initiates connection to the Controller (useful for Windows or behind firewalls).
    *   **Cloud Agents**: Dynamically provisioning agents in **Docker**, **AWS**, or **Azure** only when needed.
*   **Parallel Execution**: Using the `parallel` keyword in Pipelines to run independent stages simultaneously.

## 6. Pipeline and CI/CD
*   **Pipeline Stages**: Defining distinct phases (Build, Test, Deploy) with specific "post" behaviors (e.g., always clean workspace, notify on failure).
*   **Automated Deployment**: Integrating with tools like **Ansible**, **Terraform**, or **Kubernetes** for CD.
*   **Release Management**: Handling version tagging in Git and promoting artifacts to production repositories.

## 7. Security and Credentials
*   **Security Realms**: Determining where user data lives (Jenkins Database, LDAP, Active Directory).
*   **Credential Manager**: Securely storing SSH keys, Secret Text, and User/Pass combos so they aren't hardcoded in scripts.
*   **Auditing**: Tracking who changed what job and when using plugins like **Audit Trail**.

## 8. Artifacts and Fingerprints
*   **Artifact Retention**: Defining how many old builds or artifacts to keep to save disk space.
*   **Copy Artifact Plugin**: Allowing one job to use the output (e.g., a `.war` file) generated by a previous job.
*   **Fingerprinting**: Ensuring that if a file is used in multiple jobs, Jenkins can track exactly which build produced it.

## 9. Troubleshooting
*   **Console Output**: The primary source for debugging build failures.
*   **Workspace Cleaning**: Resolving issues caused by leftover files from previous failed runs.


# Module: Building Continuous Delivery (CD) Pipelines

## 1. Pipeline Concepts & Strategy
*   **Value Stream Mapping**: Identifying the entire process from code commit to customer delivery to eliminate waste and bottlenecks.
*   **Pipeline Gates**: Quality hurdles (unit tests, security scans, manual approvals) that a build must pass to move to the next environment.
*   **Binary Reuse**: The "Build Once" principle. You compile your code once in the CI stage and promote the exact same binary (artifact) through all subsequent environments to ensure consistency.
*   **Centralized Protection**: Using **Folders**, **RBAC**, and **Shared Libraries** to ensure multiple teams can use the same Jenkins instance without interfering with each other's configurations.

## 2. Upstreams, Downstreams, and Triggering
*   **Relationships**: An **Upstream** job triggers a **Downstream** job. This creates a chain of execution.
*   **Push vs. Pull**:
    *   **Pull (Polling)**: Jenkins periodically checks the SCM for changes. (High latency, unnecessary load).
    *   **Push (Webhooks)**: The SCM notifies Jenkins immediately when a change occurs. (Recommended for real-time CI).
*   **Parameterized Trigger Plugin**: Essential for passing specific data (like a build ID or version number) from an upstream job to a downstream job.

## 3. The Jenkins Pipeline (Code-based)
*   **Benefits vs. Linked Jobs**: Pipelines provide better visualization, durability (survives Jenkins restarts), and version control (Jenkinsfile).
*   **Concurrency**: Managing how many instances of a pipeline can run at once using the `options { disableConcurrentBuilds() }` or `stages { parallel { ... } }` blocks.
*   **Visualization**:
    *   **Blue Ocean**: A modern, visual UI for Jenkins pipelines.
    *   **Build Pipeline View**: A legacy plugin view showing the flow of upstream/downstream jobs.
    *   **Dashboard View**: High-level status of multiple jobs and build statistics.

## 4. Parameters and Folders
*   **Parameters**: Making pipelines interactive.
    *   **String/Choice**: For selecting environments or versions.
    *   **File Parameter**: Allowing users to upload an executable or config for a specific test run.
*   **Folders**: Beyond organization, folders act as a **namespace**. You can set security permissions at the folder level, ensuring Team A cannot view Team B’s credentials or jobs.

## 5. Job Promotions and CD Metrics
*   **Promoted Builds**: The process of "marking" a build as ready for a higher environment (e.g., promoting a build from 'QA' to 'Staging' after manual verification).
*   **Key Performance Indicators (KPIs)**:
    *   **Cycle Time**: How long it takes to go from code commit to production.
    *   **Deployment Frequency**: How often code is successfully deployed.
    *   **Mean Time to Recovery (MTTR)**: How quickly the pipeline is fixed after a failure.
    *   **Change Failure Rate**: The percentage of deployments that cause a failure in production.

## 6. Information Radiation
*   **Radiating Information**: Using large monitors (Build Radiators) in the office or automated Slack/Email alerts to ensure the team knows the instant a "Value Stream" is broken.


# Module: CD-as-Code Best Practices

## 1. Scalable Architecture
*   **Distributed Builds Architecture**: Moving execution away from the Controller (Master) to remote Agents. This ensures the Controller remains responsive for UI and scheduling while Agents handle heavy lifting (compiling, testing).
*   **Fungible (Replaceable) Agents**: The practice of treating Agents as "cattle, not pets." Agents should be stateless and easily replaceable. If an agent becomes corrupted or slow, it is destroyed and a fresh one is provisioned.
*   **Master-Agent Connectors**:
    *   **SSH**: Controller initiates the connection to the agent (standard for Linux).
    *   **Inbound (JNLP/TCP)**: The agent initiates the connection to the controller (ideal for agents behind firewalls or NAT).
    *   **Websocket**: A modern alternative for inbound connections over standard web ports.

## 2. Environment and Tool Management
*   **Tool Installations on Agents**: Avoiding manual tool installation. Instead, use Jenkins **Global Tool Configuration** to auto-install tools (like Maven or NodeJS) on demand, or use **Containerization**.
*   **Cloud Agents**: Dynamically provisioning infrastructure on AWS, Azure, or GCP only when a build is queued, and terminating it immediately after completion to save costs.
*   **Containerization (Docker/Kubernetes)**: Running build steps inside ephemeral Docker containers. This ensures the build environment is identical every time and eliminates "dependency hell" between different jobs on the same agent.

## 3. Reliability and Governance
*   **Traceability**: The ability to link a production deployment back to a specific Git commit, build log, and set of test results. This is achieved through **Fingerprinting** and **Artifact** management.
*   **High Availability (HA)**: Ensuring the Jenkins Controller remains operational. This involves using load balancers, shared storage for `$JENKINS_HOME`, and redundant controller instances to prevent a single point of failure.
*   **Automatic Repository Builds**: Using **Multibranch Pipelines** or **Organization Folders**. Jenkins automatically scans your GitHub/Bitbucket organization, discovers new repositories and branches containing a `Jenkinsfile`, and creates the corresponding jobs automatically.

## 4. Best Practices Summary
*   **Keep Logic in the Jenkinsfile**: Avoid configuring jobs in the UI; define the entire pipeline in code.
*   **Use Shared Libraries**: Move common code (like Slack notifications or standard deployment steps) into a central repository to reduce duplication across pipelines.
*   **Security First**: Use the **Credentials Plugin** to inject secrets at runtime rather than hardcoding them in the `Jenkinsfile`.
