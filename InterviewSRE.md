# Sr SRE Interview Guide: Comprehensive Technical & Scenario-Based Questions

## SECTION 1: INFRASTRUCTURE AS CODE (IaC)

### A. Foundational Questions

#### 1. Terraform Basics & State Management
- What is Terraform, and how does it differ from Ansible and CloudFormation in terms of declarative vs. imperative approaches?
- Explain the Terraform state file. Why is it critical, and what are the risks of state file corruption or loss?
- How do you manage Terraform state in a multi-team environment? Discuss state locking, remote backends, and best practices.
- What is the difference between `terraform plan`, `terraform apply`, and `terraform destroy`? When would you use each in production?
- What does `terraform taint` do, and when is it useful?
- Explain Terraform meta-parameters and their use cases.
- What is the difference between `count` and `for_each` in Terraform?

#### 2. Configuration Management & Orchestration
- Compare Terraform, Ansible, CloudFormation, and ARM Templates. When would you choose each tool?
- Explain Ansible playbooks, roles, and inventories. How does Ansible differ from Terraform?
- What are the advantages and limitations of CloudFormation for AWS infrastructure provisioning?
- Describe ARM Templates and when they are preferred over CloudFormation or Terraform.

#### 3. Advanced IaC Concepts
- What is Terraform's state locking mechanism, and how does it prevent concurrent modifications?
- How do you organize Terraform code for large enterprises with multiple environments (dev, staging, prod)?
- Explain Terraform modules: purpose, creation, and best practices for sharing across teams.
- What strategies would you use for version controlling IaC code and managing secrets in Terraform?
- How do you mask sensitive information like passwords in Terraform?
- How do you rotate SSL certificates and secrets using IaC?

### B. Scenario-Based Questions

#### Scenario 1: Multi-Environment Infrastructure Drift
Your team discovered that production resources were manually modified via AWS console, causing drift from IaC definitions and compliance violations. Your manager is concerned about the 15-person team's ability to maintain consistency.

- How would you detect this drift continuously and automatically? (Discuss `terraform plan` in CI/CD, PolicyAsCode tools like Sentinel, OPA, CloudGuard)
- What preventive measures would you implement to prevent manual modifications? (Discuss IAM policies, resource locks, automation via CI/CD, audit logging)
- Walk through a process to remediate drift safely without downtime. (Discuss `terraform import`, state refresh, apply strategies, rollback plans)
- How would you educate your team and establish SLIs/SLOs around infrastructure consistency?

#### Scenario 2: Terraform State Management Crisis
Your prod Terraform state file is corrupted or lost. You have 200+ resources across multiple AWS accounts and GCP projects. `terraform apply` and `terraform destroy` are both risky without valid state.

- What immediate steps would you take to recover or reconstruct the state? (Discuss state recovery from backups, manual `terraform import`, recreating state from cloud inventory)
- How would you prevent this from happening again? (Discuss remote state backends: S3 with versioning, Terraform Cloud, state locking, encryption, access controls, DR drills)
- What is your strategy for testing Terraform changes safely before applying to production?
- What does `terraform state file got corrupted` mean, and what's your recovery plan?

#### Scenario 3: Managing Secrets & Credentials in IaC
A junior engineer accidentally committed AWS credentials and database passwords to Git. Compliance flagged this as a security incident.

- How do you manage secrets securely in Terraform? (Discuss AWS Secrets Manager, HashiCorp Vault, Ansible Vault, Azure Key Vault, environment variables, encrypted tfvars)
- What detective controls would you put in place to prevent secrets from leaking? (Discuss git pre-commit hooks, secret scanning tools like TruffleHog, GitGuardian, CI/CD checks)
- Walk through your remediation process for incident involving leaked secrets.
- What security measures would be taken while provisioning infrastructure using Terraform?

#### Scenario 4: Scaling IaC Across 50+ Microservices
Your organization is transitioning 50+ microservices to cloud-native, containerized architecture. Each team is managing their own Terraform code independently without standardized processes.

- How would you structure Terraform modules to support a standardized, scalable approach? (Discuss monorepo vs. multi-repo, module registry, shared modules for VPC, EKS, RDS, observability stacks)
- What governance and policy-as-code approach would you implement? (Discuss Terraform Cloud/Enterprise, Sentinel policies, cost controls, tagging standards, compliance checks)
- How would you manage dependencies between services (e.g., database credentials passed to application Terraform)?
- What's your strategy for safe, coordinated deployments across 50+ teams?
- What's your module code structure, and how would you write sample modules?

#### Scenario 5: Multi-Environment Terraform Management
You need to manage infrastructure across dev, staging, and production environments using the same Terraform code.

- How do you manage multi-environment setup in Terraform?
- How would you structure your code to avoid duplication across environments?
- What's your approach to environment-specific configurations and variables?

#### Scenario 6: Disaster Recovery & Cross-Region Failover
You need to design disaster recovery with automated failover from us-east-1 to us-west-2. Your Terraform code must support multi-region deployments.

- How would you structure your Terraform to support multi-region deployments? (Discuss provider aliases, modules for reusability, data synchronization, secrets replication)
- What automation would trigger a failover, and how would you test it regularly?
- How would you manage DNS failover during regional outage?

---

## SECTION 2: CLOUD PLATFORMS — AWS, AZURE, GCP

### A. Foundational Questions

#### 1. AWS (EC2, VPC, IAM, RDS, S3, Lambda, ALB/NLB)
- What is a VPC, and how do you design a multi-tier VPC architecture with public/private subnets and NAT gateways?
- Explain AWS IAM roles, policies, and best practices for least-privilege access.
- How do you ensure data encryption for RDS databases (at rest and in transit)?
- What are the differences between Application Load Balancer (ALB), Network Load Balancer (NLB), and Classic Load Balancer?
- Describe S3 lifecycle policies, versioning, and security best practices.
- What is VPC peering, and how does it differ from VPN and AWS Direct Connect?
- How do you manage DNS entries in AWS environment?
- What is a Transit Gateway, and how does it compare to VPC peering?
- Explain the differences between Security Groups (SG) and Network ACLs (NACL).
- What are EBS, S3, and databases, and when would you use each?

#### 2. Azure (VNets, Storage, App Service, AKS, Azure DevOps)
- What is an Azure VNet, and how do you configure subnets, UDRs, and NSGs?
- Explain Azure RBAC, managed identities, and least-privilege access implementation.
- How do you secure Azure Storage accounts (encryption, access control, firewall rules)?
- What are the differences between Azure App Service, App Service Plans, and Azure Container Instances?

#### 3. GCP (Compute Engine, Cloud SQL, Firestore, GKE, Cloud Load Balancer)
- What is Google Cloud's resource hierarchy (organizations, folders, projects), and how do you manage IAM and billing?
- Explain the differences between Compute Engine instances, App Engine, and Cloud Functions.
- How do you design a GCP VPC with subnets, firewall rules, and Cloud NAT?
- What are the key differences between Cloud SQL and Firestore, and when would you use each?

### B. Scenario-Based Questions

#### Scenario 1: Multi-Cloud Hybrid Architecture
Your company has applications in AWS, Azure, and GCP. You're tasked with establishing centralized observability, security, and compliance across all three clouds.

- How would you design a unified logging, metrics, and tracing architecture across clouds?
- What's your approach to identity and access management across clouds?
- How would you establish cost visibility and controls across clouds?
- Walk through your strategy to migrate workloads off legacy clouds.

#### Scenario 2: Cloud Cost Explosion
Your AWS bill doubled in three months. Your manager wants 40% cost reductions without impacting performance.

- How would you systematically identify cost optimization opportunities?
- What automation would you implement to prevent future cost overages?
- How would you communicate cost implications to engineering teams?
- What cost optimization strategies would you implement?

#### Scenario 3: AWS Architecture in Current Environment
Explain your organization's AWS architecture, including VPC design, peering, site-to-site connectivity, and DNS management.

- What is the current architecture you're using?
- How do you handle VPC peering and site-to-site tunnel connectivity?
- How do you manage DNS entries and DNS resolution?
- What is a DNS resolver in Route 53, and how does resolution work if the domain is maintained outside Route 53?

#### Scenario 4: Data Residency & Compliance
Your company has operations in EU, requiring GDPR compliance. Data is currently in us-east-1. You need to keep EU customer data in EU regions while maintaining global redundancy.

- How would you partition your database and object storage to comply with data residency requirements?
- What's your approach to encryption, audit logging, and compliance monitoring?
- How would you manage secrets securely across regions?

#### Scenario 5: API Rate Limiting & Throttling
Your application resources suddenly got throttled because an API is not working and will be down for 24 hours. How would you handle this?

- What's your strategy when external API rate limits are exhausted?
- How would you prevent cascading failures when API limits are hit?
- What mitigation strategies would you implement?

---

## SECTION 3: NETWORKING — TCP/IP, DNS, LOAD BALANCING, VPNs

### A. Foundational Questions

#### 1. TCP/IP Fundamentals
- Explain the OSI model and how TCP/IP fits within it. Describe the three-way TCP handshake.
- What is the difference between TCP and UDP? When would you use UDP in SRE context?
- Explain IP addressing, subnetting, and CIDR notation.
- What is NAT (Network Address Translation), and how does it work?

#### 2. DNS (Domain Name System)
- How does DNS resolution work? Walk through the steps from URL to response.
- Explain DNS record types (A, AAAA, CNAME, MX, TXT, SRV, NS). When would you use each?
- What is DNS propagation, and how do you verify it?
- Describe DNS caching and TTL. What are the trade-offs of short vs. long TTL?
- How do you implement health checks and failover using DNS (e.g., Route53)?

#### 3. Load Balancing
- Explain Layer 4 (L4) vs. Layer 7 (L7) load balancing. When would you use each?
- Describe load balancing algorithms: round-robin, least connections, IP hash, weighted, random.
- What are sticky sessions, and when are they necessary?
- How do you troubleshoot asymmetric routing and connection imbalance issues?
- Explain connection pooling and its importance in load-balanced environments.

#### 4. VPNs & Peering
- How does a VPN work? Explain IPsec and TLS/SSL-based VPNs.
- What is VPC peering, and how does it differ from VPN and AWS Direct Connect?
- Explain AWS VPN (Site-to-Site and Client VPN) and Azure VPN Gateway.
- How do you ensure secure communication over a VPN or peering connection?

### B. Scenario-Based Questions

#### Scenario 1: DNS Propagation & Failover Crisis
You migrated a critical service from Data Center A to AWS. Despite updating DNS records, 2% of traffic still goes to the old data center due to DNS caching.

- How would you have planned and executed this migration to minimize DNS issues? (Discuss TTL reduction, health checks, gradual traffic shifting, weighted routing)
- What's your strategy for detecting stale DNS cache issues in real-time?
- How would you handle the 2% stuck traffic during migration?

#### Scenario 2: Load Balancer Asymmetry & Connection Imbalance
Your application is deployed across 5 EC2 instances behind an ALB. One instance receives 40% of traffic while others receive 12% each.

- What are the root causes of this imbalance? (Discuss sticky sessions, cross-zone load balancing, target weights, persistent connections)
- How would you diagnose this issue? (Discuss ALB access logs, target metrics, connection profiling)
- What corrective actions would you take?
- How would you prevent this from happening again?

#### Scenario 3: Multi-Region Failover with DNS
Your primary region (us-east-1) experiences a partial outage. You need to failover to eu-west-1 while keeping primary in read-only mode.

- How would you implement DNS-based failover automation? (Discuss Route53 health checks, failover routing policies, Lambda-based failover triggers)
- What's your data synchronization strategy?
- How do you test failover regularly without impacting customers?
- What's your communication plan to stakeholders during failover?

#### Scenario 4: VPN Performance Degradation
Remote employees in Asia connecting via Site-to-Site VPN are experiencing 200ms+ latency and 3-5% packet loss during business hours.

- How would you diagnose the root cause? (Discuss VPN metrics, packet capture, path analysis, ISP backbone congestion)
- What optimization strategies would you consider? (Discuss VPN redundancy, QoS policies, DirectConnect for critical users)
- What's your long-term architecture to support global remote workers?

#### Scenario 5: Application Latency Issues
Your application is facing latency issues. How would you troubleshoot and fix it?

- If an application hosted in a virtual machine responds slow, how would you troubleshoot?
- What are your diagnostic steps and remediation strategies?

---

## SECTION 4: MONITORING & OBSERVABILITY

### A. Foundational Questions

#### 1. Observability Fundamentals
- What are the three pillars of observability: metrics, logs, and traces? How do they differ?
- Explain the difference between monitoring and observability.
- What are the Golden Signals in SRE (latency, traffic, errors, saturation)?
- Describe cardinality problems in metrics and their impact on observability systems.

#### 2. Prometheus & Grafana
- How does Prometheus work? Explain scraping, scrape intervals, and retention.
- What is a Prometheus exporter? Name common exporters (node-exporter, kube-state-metrics).
- Explain PromQL. Write a query to calculate the 95th percentile latency.
- What are recording rules and alerting rules in Prometheus?
- How do you design effective Grafana dashboards? What are best practices?
- Explain Prometheus service discovery (static configs, Kubernetes SD, cloud SD).

#### 3. Splunk
- What is Splunk, and how does it differ from Prometheus/Grafana?
- Explain Splunk data indexing and search language (SPL).
- How do you aggregate logs from 1,000+ servers into Splunk?
- Describe Splunk alerting and webhooks.

#### 4. Dynatrace & AppDynamics
- What is APM (Application Performance Monitoring)?
- Explain distributed tracing and its importance in microservices.
- How does Dynatrace auto-instrumentation differ from manual instrumentation?
- Describe service maps and dependency tracking.

#### 5. OpenTelemetry
- What is OpenTelemetry, and why is it important for vendor-agnostic observability?
- Explain the OpenTelemetry architecture: SDK, API, collector, exporters.
- How would you instrument a Python or Java application with OpenTelemetry?
- What exporters would you use to send telemetry to Prometheus, Jaeger, and Datadog simultaneously?

### B. Scenario-Based Questions

#### Scenario 1: Observability at Scale
Your organization has 200+ microservices, 500+ servers, and 10M+ requests/second. Your observability stack consumes 20% of infrastructure budget and is difficult to manage.

- How would you optimize cardinality and reduce storage costs?
- What's your strategy for alert fatigue? (Discuss alert quality, SLO-based alerting, runbook automation)
- How would you unify observability across 200+ services?
- Walk through a complex incident investigation using metrics, logs, and traces.

#### Scenario 2: SLO-Driven Observability
Your team has defined SLOs (99.9% availability, p99 latency < 200ms, error rate < 0.1%). However, alerting is based on individual metrics, not SLO violations, resulting in alert fatigue.

- How would you redesign observability and alerting to be SLO-driven? (Discuss SLO instrumentation, burn rate calculations, multi-window alerting)
- What's your monitoring strategy for upstream dependencies?
- How would you track and communicate SLO performance to leadership?

#### Scenario 3: Distributed Tracing Instrumentation
You're implementing OpenTelemetry across 50 services written in Java, Python, Go, and Node.js. Some teams have partial instrumentation, others have none.

- How would you prioritize instrumentation across 50 services?
- What's your strategy for standardizing instrumentation across different languages?
- How would you validate tracing quality and catch instrumentation gaps?
- Walk through a performance investigation using distributed traces.

#### Scenario 4: Cost Optimization of Observability Stack
Your Splunk and Dynatrace licenses cost $500K/year, and you're indexing 10TB of logs daily. Leadership demands a 30% cost reduction.

- How would you audit your current observability to find cost optimization opportunities?
- What's a phased approach to cost reduction without compromising visibility?
- How would you evaluate vendor consolidation (moving to Datadog or New Relic)?

#### Scenario 5: Synthetic Monitoring
What is synthetic monitoring, and how would you implement it for critical user journeys?

---

## SECTION 5: SCRIPTING — PYTHON, BASH/SHELL

### A. Foundational Questions

#### 1. Python for SRE
- Write a Python script to monitor CPU usage and send an alert if it exceeds 80%.
- How do you handle API requests in Python (requests library, error handling, retries)?
- Explain decorators in Python and how they're used for retry logic.
- Write a Python script to parse a JSON log file and extract error messages.
- How do you implement concurrent requests in Python (threading, async/await, multiprocessing)?

#### 2. Bash/Shell Scripting for SRE
- Write a bash script to monitor disk usage across multiple servers and alert if it exceeds 90%.
- Explain the differences between `source`, `sh`, and `bash`.
- How do you handle errors in bash scripts (`set -e`, `trap`)?
- Write a bash script to backup a database and upload it to S3 with retry logic.
- Explain variable scoping, arrays, and functions in bash.
- How do you list all unique IPs from logs in a Linux server?

#### 3. Scripting for Automation
- How would you automate certificate rotation for SSL/TLS certificates across 100+ servers?
- Write a script to roll back a Kubernetes deployment if health checks fail.
- How would you automate log rotation and archival across servers?
- Explain cron jobs, systemd timers, and when to use each for scheduled tasks.

### B. Scenario-Based Questions

#### Scenario 1: Incident Response Automation
When a critical alert fires (e.g., database replication lag > 30 seconds), an on-call engineer manually investigates, runs diagnostics, and attempts fixes. This takes 15–30 minutes. You want to automate it to < 2 minutes.

- How would you automate incident response steps (investigation, diagnostics, remediation) using Python/Bash?
- What safeguards would you implement to prevent automated remediation from making things worse? (Discuss pre-flight checks, circuit breakers, approval gates, rollback)
- How would you handle stakeholder communication automatically? (Discuss Slack/PagerDuty API integration, status page updates)
- How would you test and validate these automation scripts?

#### Scenario 2: Multi-Cloud Resource Inventory
Your organization has resources across AWS (300+), Azure (200+), and GCP (150+). You need a unified inventory system tracking resource metadata, tags, compliance labels, drift, and cost allocation using Python APIs.

- How would you design the data model and architecture?
- How would you handle large-scale API calls (rate limiting, pagination, error handling)?
- What reporting and alerting would you implement? (Discuss drift detection, cost tracking, compliance checks)
- How would you keep the inventory fresh (real-time vs. scheduled sync)?

---

## SECTION 6: SECURITY

### A. Foundational Questions

- What security measures will be taken while provisioning infrastructure using Terraform?
- How do you implement IAM least privilege policies?
- How do you secure containers and VMs?
- What are CNAPP (Cloud Native Application Protection Platform) tools?
- How do you rotate and manage secrets securely?
- How do you prevent secrets from being exposed in logs and code?

### B. Scenario-Based Questions

#### Scenario 1: Security Incident Management
Your application was attacked. What security practices would you follow?

- What's your immediate incident response process?
- How would you identify the attack vector?
- What post-incident actions would you take?

#### Scenario 2: Secret Management in Current Organization
Explain how secrets are managed in your current organization.

- What secret management tools do you use?
- How do you handle secret rotation?
- How do you ensure secrets are not exposed?

---

## SECTION 7: CI/CD PIPELINE & DEPLOYMENT STRATEGIES

### A. Foundational Questions

- What does a CI/CD pipeline design look like?
- What are the stages and gates you would implement?
- What are container security best practices?
- What is Blue-Green Deployment, and when would you use it for applications vs. databases?

### B. Scenario-Based Questions

#### Scenario 1: Deployment Strategies
When would you implement Blue-Green Deployment for:
- Applications
- Databases

---

## SECTION 8: SRE GOVERNANCE — SLIs, SLOs, SLAs, ERROR BUDGETS

### A. Foundational Questions

#### 1. SLI/SLO/SLA Fundamentals
- Define SLI (Service Level Indicator), SLO (Service Level Objective), and SLA (Service Level Agreement). How do they differ?
- What are the Golden Signals, and how do you translate them into SLIs?
- Explain error budgets and their role in incident management and deployment decisions.
- How do you calculate burn rate, and what are multi-window burn rate alerts?

#### 2. Designing SLOs
- How do you choose appropriate SLO targets (99.9% vs 99.99%) for different services?
- Walk through designing SLOs for an e-commerce platform (checkout, search, inventory).
- How do you measure SLOs in the presence of client-side issues or CDN failures?
- What's the relationship between SLOs and monitoring/alerting?

### B. Scenario-Based Questions

#### Scenario 1: SLO Alignment & Error Budget
Your platform has an SLO of 99.9% availability (9 hours allowed downtime per year).

- How do you capture availability of the target system?
- How would you manage error budget for deployments?
- What actions would you take if burn rate exceeds threshold?

---

## SECTION 9: SYSTEM PERFORMANCE & TROUBLESHOOTING

### A. Foundational Questions

- What are MTTF (Mean Time To Failure) and MTBF (Mean Time Between Failures)?
- How do you measure and track these metrics?

### B. Scenario-Based Questions

#### Scenario 1: CPU Utilization Issues
If an application is hosted in a virtual machine and shows CPU utilization of 200% on the VM, how do you find out and fix the issue?

- How would you debug a server where CPU utilization is 98%?
- What diagnostic tools and steps would you use?
- What are potential causes of over-100% CPU utilization?

#### Scenario 2: Heap Dump & Memory Issues
How do you troubleshoot if multiple threads get spun up and consume high resources? Discuss heap dump analysis and heap size-related questions.

- What tools would you use to analyze heap dumps?
- How do you identify memory leaks?
- What's your remediation strategy?

#### Scenario 3: System Response Time
If a system responds slow to a request, how would you troubleshoot?

- What are your diagnostic steps?
- How do you isolate whether the issue is application, infrastructure, or network-related?
- What metrics would you check?

#### Scenario 4: TLS Version Compatibility
Your entire application uses TLS 1.2. A third-party tool requires TLS 1.3. If you upgrade, your application won't support TLS 1.2 clients. How do you approach and fix this issue?

- What's your compatibility strategy?
- How would you handle clients still using TLS 1.2?
- What's your phased migration approach?

---

## SECTION 10: BUSINESS CONTINUITY & DISASTER RECOVERY

### A. Foundational Questions

- Explain BCP (Business Continuity Planning) principles.
- What are RTO (Recovery Time Objective) and RPO (Recovery Point Objective)?
- What are the differences between backup, disaster recovery, and high availability?
- How do you design for high availability and scalability?

### B. Scenario-Based Questions

#### Scenario 1: 3-Tier Architecture with HA and Scalability
Design a 3-tier architecture with high availability and scalability. Include:
- Load balancing and traffic routing
- Database design
- Failover mechanisms

---

## SECTION 11: CONFIGURATION MANAGEMENT & PRODUCTION EXCELLENCE

### A. Foundational Questions

- Explain configuration management and how you ensure production configuration consistency.
- What are best practices for infrastructure setup and architecture?
- What services do you use daily for infrastructure management?
- Best practices for Terraform and infrastructure provisioning.

### B. Scenario-Based Questions

#### Scenario 1: Incident Management
- What is the major incident that you handled?
- How would you handle if an outage is detected by customers before your monitoring systems detect it?

---

## SECTION 12: LEADERSHIP & MENTORSHIP

### A. Questions

- How does SRE differ from DevOps Engineer?
- How would you mentor junior engineers?
- How would you plan to create AI agent bots to automate daily processes? In which scenarios would you implement them?
- Given unlimited budget and resources, what system improvements would you prioritize?
