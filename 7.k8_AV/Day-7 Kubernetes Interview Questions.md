# DevOps Day 36: Kubernetes Interview Questions (Part 1 — 10 Questions)

## Why this video (and how to use it)

# - - Speaker introduces **Day 36**: *“Kubernetes interview questions part one.”*
- Notes the original plan was practical **Services** implementation + intro to **Ingress**, but he switched to interview questions because the last 5–6 days covered many interview-relevant topics.
- Frames this as a self-assessment / mock interview:
- He will show each question first (no answer immediately).
- Viewer should pause and try answering before he reveals the answer.
- Viewers can comment their score (no competition—used as feedback/retrospective).

## Question 1: Difference between Docker and Kubernetes

# - - Expected framing:
- *Docker is a “container platform”; Kubernetes is a “container orchestration platform.”*
- Why Kubernetes vs Docker (his answer points):
- Containers are *“ephemeral in nature”*—they can go down; if a container goes down, the application goes down.
- Kubernetes provides:
- **auto-healing**
- **auto-scaling**
- **cluster behavior** (combine multiple VMs into a cluster; if a node goes down, pods can move to another node)
- **enterprise capabilities** (e.g., load balancing)
- extensibility via **custom resources / controllers** (example mentioned: **Ingress controllers**)

## Question 2: Main components of Kubernetes architecture

# - - High-level split:
- **Control plane** + **Data plane**
- Control plane components (as he lists):
- **API server**: handles APIs / talks to end users
- **Scheduler**: schedules resources on the cluster
- **etcd**: *“Kubernetes object store”* where resources are stored as objects
- **Controller manager**: manages default controllers (mentions ReplicaSet / replication controller)
- **Cloud controller manager**: cloud-provider integration logic

### Cloud controller manager example (LoadBalancer service on AWS)

# - - If you create a **Service of type LoadBalancer**:

- AWS-specific logic in cloud controller manager helps spin up a load balancer IP.
- Contribution model:

- Cloud providers contribute logic to cloud controller manager.
- If he built his own cloud, he’d need to contribute so services can get load balancer IPs there.
- Data plane components (primary three he emphasizes):

- **kubelet**
- **kube-proxy**
- **container runtime**
- Mentions some people also cite **CoreDNS**, but he says you can restrict to the three above.

### Data plane component roles (as he explains)

# - - **kubelet**: manages pods on nodes; restarts pods when needed.
- **kube-proxy**: networking component; updates **iptables** rules (example: NodePort routing).
- **container runtime**: required to run containers (Java runtime analogy).

### Docker runtime support clarification

# - - He notes:
- Kubernetes can use Docker shim / containerd / CRI-O.
- “Nothing is supported out of the box” now—you must install a container runtime on every node.
- Kubernetes still supports Docker; it’s just not automatically provided on nodes by default.

## Question 3: Differences between Docker Swarm and Kubernetes

# - - He says this wasn’t covered earlier but is frequently asked.
- His comparison:
- Kubernetes is more popular in the market vs other orchestration options (mentions Cloud Foundry, Mesos/Marathon, Docker Swarm).
- **Docker Swarm**: Docker-based, easy to install/use; suited for smaller/simple workloads.
- **Kubernetes**: better for mid/large scale; more scaling options and advanced networking.
- Mentions networking options like **Flannel**, **Calico**, **SDN OVN**.
- Kubernetes has strong third-party ecosystem support (mentions **CNCF** community).
- Kubernetes extensibility via **Custom Resource Definitions (CRDs)**:
- People can write controllers to extend Kubernetes capabilities.

## Question 4: Difference between a Docker container and a Kubernetes pod

# - - His core phrasing:
- *A pod is “a runtime specification” of a container written in a YAML file.*
- Key differences he highlights:
- Pod is the lowest-level deployment unit in Kubernetes.
- A pod can run **one container or multiple containers**.
- With multiple containers in one pod:
- they share the same network
- they can share storage/resources in the pod

## Question 5: What is a namespace in Kubernetes?

# - - Motivation:
- A Kubernetes cluster is shared by multiple people/projects.
- You typically don’t create a separate production cluster per project.
- Definition (his phrasing):
- *A namespace is “logical isolation of resources.”*
- Example:
- Project A gets namespace A; Project B gets namespace B.
- Teams work within their namespaces on the same physical cluster.
- Isolation mechanisms he mentions:
- **RBAC (Role-Based Access Control)** for access restriction
- separate network policies
- restrict namespace A developers from accessing namespace B resources

### ### RBAC (Role-Based Access Control)

# - - Introduces RBAC as a way to enforce namespace isolation (details later).

## Question 6: Role of kube-proxy

# - - He expands beyond architecture summary:
- kube-proxy configures network rules on each node.
- Under the hood for **NodePort** services, kube-proxy updates **iptables** so:
- traffic to `NodeIP:Port` gets routed to the correct pod.
- Notes:
- other modes exist (mentions **ipvs**), but default is **iptables**.

## Question 7: Different types of Kubernetes Services

# - - He ties services back to their responsibilities:
- load balancing
- service discovery
- exposing apps externally
- Service types (as he lists):
1. **ClusterIP**
2. **NodePort**
3. **LoadBalancer**
- Brief differences (his explanation):
- **ClusterIP**: accessible only within the Kubernetes cluster.
- **NodePort**: accessible via `NodeIP:NodePort` to users who can reach the nodes (e.g., in an AWS VPC/EC2 context).
- **LoadBalancer**: cloud controller manager provisions a public/load balancer IP so anyone can access externally.
- Mentions:
- exposing can also be done via **Ingress**, but keeps this answer scoped to services.

## Question 8: Difference between NodePort and LoadBalancer

# - - He says this is frequently asked; explanation mirrors Question 7:
- **NodePort**: internal org/network users who can reach worker node IPs.
- **LoadBalancer**: creates/provides a public load balancer IP (external access).

## Question 9: Role of kubelet

# - - kubelet is responsible for managing pod lifecycle on worker nodes.
- Lifecycle chain he describes:
- Scheduler places pod on a worker node.
- kubelet monitors pod health continuously.
- If a pod goes down, kubelet notifies the API server.
- API server notifies ReplicaSet/Deployment controller.
- Controller ensures desired replica count is restored.

## Question 10: Day-to-day activities on Kubernetes (DevOps engineer)

# - - He says people struggle with this but it’s straightforward.
- Example answer elements he provides:
- manage Kubernetes clusters for the organization
- ensure applications are deployed and running without issues
- set up monitoring for the cluster
- troubleshoot issues for developers (pods/services/traffic routing problems)
- perform maintenance:
- version upgrades (e.g., worker node upgrades)
- install mandatory packages
- keep nodes protected from security vulnerabilities
- act as Kubernetes subject matter expert:
- handle Jira items/tickets and help teams resolve issues and understand concepts

## Close + what’s next

# - - He asks viewers to check how many of the 10 they got correct (most questions were already covered in prior videos).
- Upcoming topics he previews:
- Ingress
- practical Services implementation
- Custom Resource Definitions
- Helm
- Kubernetes interview questions part 2
