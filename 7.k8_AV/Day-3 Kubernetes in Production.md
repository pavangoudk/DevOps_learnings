# DevOps Day 32: Kubernetes in Production (Distributions + Cluster Lifecycle with kOps)

## Intro: what “production systems” means here

# 

- Speaker apologizes for the upload delay, then introduces **Day 32**.
- Topic: **Kubernetes production systems**—how DevOps engineers manage the Kubernetes cluster lifecycle in production:
- creation
- upgradation
- configuration
- deletion
- Why this matters:
- Many learners practice on **minikube** and other local setups, which are great for learning—but are **development environments**, not production.
- For DevOps/Kubernetes admin interviews, you’re expected to know how clusters are managed in real organizations.

## Local Kubernetes setups are for development, not production

# 
- Examples of local/dev Kubernetes environments he lists:
- minikube
- k3s
- kind
- k3d
- microk8s
- He emphasizes:
- these are “developer environments.”
- minikube documentation explicitly says it’s a local cluster and **should not be used in production**.
- these tools don’t usually support production patterns like **high availability**.

## Why interviews focus on “distributions,” not minikube

# 
- He states interviewers will ask:
- what **Kubernetes distribution** you’ve used in production
- whether you managed installation and upgrades
- So you need to know what distributions are and which are common.

## What is a “distribution”? (Linux analogy)

# 
- Uses Linux as the comparison:
- Linux is open-source and free.
- Companies build “distributions” on top of it:
- Amazon Linux
- Red Hat
- CentOS
- Ubuntu
- Benefit of distributions (as he frames it):
- support and timely security patching, especially when you’re paying (e.g., Red Hat subscription).

### Definition (implied by his explanation)

# 
- *A distribution is when someone takes an open-source platform and builds “wrappers”/enhancements on top of it, often providing support and a better user/customer experience.*

## Kubernetes distributions: what they are and why they exist

# 
- Kubernetes is described as:
- *“an open source container orchestration solution platform… container orchestration engine.”*
- Companies build Kubernetes distributions to improve user experience and provide support.
- Examples he gives:
- **Amazon EKS (Elastic Kubernetes Service)**
- **Red Hat OpenShift**
- **VMware Tanzu**
- **Rancher** (Rancher Labs)
- Support/cost framing example:
- If you install Kubernetes yourself on EC2 and face Kubernetes issues, AWS will only support the EC2 layer.
- For Kubernetes support, AWS points you to the managed service (**EKS**).

## Which distributions are popular (his stated order)

# 
- He says he researched and found this general order of production usage:
1. **Kubernetes** (upstream) itself
2. **OpenShift**
3. **Rancher**
4. **VMware Tanzu**
5. **EKS / AKS / GKE**
6. **Docker Kubernetes Engine**
- He clarifies:
- These are Kubernetes distributions.
- **Docker Swarm** is not included because it’s a different orchestration engine (not a Kubernetes distribution).

## Why some orgs still run upstream Kubernetes in production

# 
- He addresses: “If Kubernetes is open source, why use it in production?”
- Reasons he gives:
- Cost: large orgs with many clusters/nodes and many developers can’t spin up a managed cluster per developer team (e.g., EKS would be too expensive at scale).
- Some orgs don’t require strict “fix in one hour” support timelines.
- So many organizations use upstream Kubernetes directly, especially in staging/pre-prod and sometimes production.

## Kubernetes vs EKS (difference explained)

# 
- Core difference:
- Self-managed Kubernetes on EC2: **you** manage it; AWS won’t support Kubernetes misconfig/issues.
- EKS: AWS provides a **managed and supported** Kubernetes service.
- He adds that EKS includes extra wrappers/plugins/tools (mentions **eksctl**) but “end of the day the solution is the same.”

## The tool focus: kOps for production cluster lifecycle

### ### kOps (Kubernetes Operations)

# 

- He introduces the main production-focused tool for this lesson: **kOps**.
- Notes other tools exist:
- kubeadm
- kubespray
- Why kOps (his framing):
- The key DevOps responsibility is not only installation, but lifecycle:
- upgrades
- modifications
- deletion
- kOps is widely used for managing that lifecycle more smoothly than kubeadm (which he says involves more manual work and less smooth upgrade/config handling).
- Mentions OpenShift installs often use **Ansible playbooks** from OpenShift docs and require Red Hat subscription (and may cost money).

## Demo setup + cost disclaimer (AWS resources)

# 
- Before demo, he warns this can be chargeable because it may create:
- EC2 instances
- EBS volumes (he mentions 8 GB)
- S3 buckets
- Route 53 hosted zone (if using a custom domain)
- Suggests:
- if you don’t want to spend money, just understand the process.
- he mentions he’ll show a “trick” to create a cluster without paying (he doesn’t detail the trick further in the transcript).

## Practical process overview (what he walks through)

### ### GitHub repo (steps source)

# 

- He references a GitHub repository called **Kubernetes Zero to Hero** that contains step-by-step instructions.

### ### Prerequisites

# 
- Needs:
- **Python 3** (for AWS CLI)
- **AWS CLI**
- **kubectl** (only needed if you want to interact with the cluster after install)
- Suggests doing this either:
- on your laptop, or
- on an Ubuntu EC2 instance (he notes they used Ubuntu in prior videos)

### ### Install kOps

# 
- He indicates kOps is installed via the commands in the repo.
- Shows a quick verification idea: running `kops` confirms it’s installed.

### ### AWS credentials setup

# 
- Uses AWS CLI configuration:
- run `aws configure`
- provide access key ID, secret access key, default region, output format
- Explains where to get credentials in AWS console:
- Security credentials → Access keys
- Permissions guidance:
- If not using admin, grant IAM permissions:
- EC2 full access
- S3 full access
- IAM full access
- VPC full access
- If using admin user, permissions are already present.

### ### S3 bucket for kOps state

# 
- kOps requires an **S3 bucket** to store Kubernetes cluster configuration/state.
- He demonstrates creating an S3 bucket and warns bucket names must be unique.

### ### Create cluster command (structure explained)

# 
- He breaks down the kOps create command inputs:
- cluster name
- S3 state store (bucket)
- zone/region
- node count
- instance type (e.g., t2.micro)
- EBS volume size
- Domain note:
- He uses a cluster name like `k8s.local` (local domain) for demo.
- In production, use a real domain (e.g., company domain) and configure it in **Route 53**.

### ### Apply/update cluster configuration

# 
- After creating the cluster configuration, you must run a final command to apply it (he points to a “one specific command” that finalizes cluster creation).
- He does **not** execute the cluster creation fully in the demo because he already has AWS resources incurring cost.

## Route 53 hosted zone (production domain setup)

### ### Route 53

# 

- If using a real domain, you must create/configure a hosted zone in Route 53.
- He shows a simple command template to create the hosted zone and says:
- replace `dev.example.com` with your domain (e.g., `amazon.com` style example)
- Cost-saving/local option:
- use `.k8s.local` for dev/staging instead of buying/configuring a custom domain.

## Guidance for learners: use minikube for the course exercises

# 
- He advises:
- Don’t do day-to-day course experiments on kOps-created clusters because AWS billing applies.
- For learning from tomorrow onward, practice on **minikube**.
- If you have free AWS credits, you can practice on the kOps cluster instead.

