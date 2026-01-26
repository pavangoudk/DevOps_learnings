# DevOps Day 30: Kubernetes Basics—Why It Matters + Docker vs Kubernetes

## Intro: “Kubernetes is easy” and why it’s the key DevOps skill

# 

- Speaker introduces **Day 30** and starts the **Kubernetes journey**.
- Opens with the claim: Kubernetes is easy, and many people over-worry about it.
- Observes many DevOps learners focus heavily on **CI/CD pipelines**, but says:
- Kubernetes is the “key player” and appears in essentially all DevOps job descriptions.
- *“Kubernetes is the future… the future of DevOps.”*
- Career framing:
- If you want only a short “sprint” into DevOps, CI/CD-focused roles exist (he calls them closer to “build and release engineering”).
- If you want a long “marathon” DevOps career, Kubernetes is essential.

## Why Kubernetes became necessary (microservices + containers context)

# 
- Notes industry shift to **microservices** for the last 6–7 years.
- Recaps that the last several classes were about **Docker and containers**.
- Prerequisite statement:
- *“The only prerequisite for learning Kubernetes is Docker.”*
- Clarifies he really means **containers fundamentals**, but people relate to “Docker” as the familiar term.
- What “strong basics” means (not just Docker commands):
- differences: containers vs virtual machines
- networking isolation
- namespace isolation
- why containers are lightweight
- securing containers (distroless, multi-stage builds)

## Key starter question: Docker vs Kubernetes

# 
- He re-anchors what Docker is (from prior class):
- Docker is a **container platform** that makes container lifecycle work easy via Docker engine/CLI.

### Definition (textbook)

# 
- *“Kubernetes is nothing but a container orchestration platform.”*
- He immediately says the one-line definition isn’t enough for beginners, so he explains practical differences.

## The practical problems with “just Docker” (why orchestration is needed)

# 
- Returns to a core container property:
- *“Containers are ephemeral in nature”* and he redefines it:
- *“Ephemeral is… short living in nature.”*

### Problem 1: Docker is “single host” in scope

# 
- Example: one host with Docker running **100 containers**.
- If one container starts consuming lots of memory, other containers may fail to start or die.
- He explains at a high level that Linux/kernel decisions determine which processes get killed under memory pressure.
- Takeaway: on a single host, containers can impact each other due to shared resources.

### Problem 2: No built-in auto-healing

# 
- Scenario: someone kills a container.
- Result: the application becomes inaccessible until a user/DevOps engineer intervenes.
- He names the desired behavior:
- *“Auto healing is a behavior where without user’s manual intervention… your container should start by itself.”*
- Docker on a laptop doesn’t do that automatically; in production with “10,000 containers,” manual monitoring (e.g., constantly running `docker ps`) is unrealistic.

### Problem 3: No built-in auto-scaling (plus load balancing requirement)

# 
- Example: host has 4 CPU / 4 GB RAM (simplified).
- Traffic spike scenario:
- user load jumps from 10,000 to 100,000 during festivals or big releases (Netflix/Marvel example).
- Two scaling approaches he describes:
1. manually increase container count (e.g., 1 → 10)
2. scale automatically when load increases
- He stresses you also need **load balancing**:
- users can’t be told to manually pick different container IPs; they just hit `netflix.com`.
- a load balancer must distribute traffic across replicas.

### Problem 4: Docker alone lacks “enterprise-level support”

# 
- He calls Docker a “minimalistic / simple platform.”
- Says enterprise apps need capabilities such as:
- load balancers
- firewalls
- scaling support
- auto-healing support
- API gateways
- whitelisting/blacklisting (e.g., block suspected DDoS sources)
- His conclusion:
- Docker alone isn’t “enterprise ready” by default (unless you go to higher-level Docker concepts like Swarm).
- *“Docker is never used in production”* (he qualifies that Swarm may be used, but Docker alone isn’t).

## How Kubernetes solves these Docker problems (high level)

# 
- He lists the four problems again and maps Kubernetes to them.

### Kubernetes is a cluster (solves single-host limitation)

# 
- Kubernetes is “by default” a **cluster**:
- *“Cluster is basically group of nodes.”*
- Production setup described as a **master node architecture** (one master + multiple nodes).
- He notes you can run Kubernetes on one node for learning, but production is generally clustered.
- How it helps:
- if one node/container is causing resource pressure, Kubernetes can place workloads (“pods”) on another node.

### ReplicaSets / ReplicationController + YAML (manual scaling foundation)

# 
- Kubernetes is “dependent on YAML files.”
- Mentions controllers:
- **ReplicationController** (older)
- **ReplicaSet** (newer)
- also references **Deployment** YAML
- Manual scaling idea:
- update YAML to increase replicas from 1 to 10 before an expected festival/load spike.

### HPA (Horizontal Pod Autoscaler) (auto-scaling)

# 
- Names HPA explicitly:
- **Horizontal Pod Autoscaler (HPA)**
- Example behavior:
- if load crosses a threshold (he cites 80%), Kubernetes spins up another container/pod and continues scaling as needed.

### Auto-healing (replace failing containers/pods)

# 
- Describes Kubernetes auto-healing behavior:
- Kubernetes detects a container/pod going down and starts a replacement “even before” the old one fully goes down (as he frames it).
- Mentions a key component (without deep dive yet):
- **API server** receives signals/understands state and triggers action.
- Notes Kubernetes usually talks in terms of **pods**, though he’s using “container” wording for now.

## Enterprise support: where Kubernetes comes from and how it evolves

# 
- Origin story he gives:
- Kubernetes originated from Google; Google used a tool called **Borg**.
- Kubernetes is described as “one of the parts” / initial solution direction related to Borg (Borg itself is not open source).
- Kubernetes aims to be enterprise-grade, but he adds an important caveat:
- Kubernetes does **not** solve everything “100%” out of the box.
- It’s evolving under the **Cloud Native Computing Foundation (CNCF)** with many contributors.

### ### CNCF ecosystem and extensibility

# 
- Mentions CNCF-backed tools/projects (examples he names):
- Podman
- Buildpacks
- Prometheus
- Explains extensibility mechanism:
- **Custom Resources (CR)**
- **Custom Resource Definitions (CRDs)**
- *These let you “extend Kubernetes to any level.”*

### Ingress Controllers example (load balancing gap → extension)

# 
- Notes Kubernetes’ default load balancing is basic:
- Services + kube-proxy do simple (e.g., round-robin) load balancing.
- How the ecosystem addresses advanced load balancing:
- Kubernetes enabled extensions; tools like **NGINX** provide **Ingress controllers** to bring richer load balancing behavior into Kubernetes.
- Mentions adoption reality:
- companies migrate to Kubernetes gradually as support and ecosystem mature.

## Closing: roadmap for the next classes

# 
- Reassures again: Kubernetes is easy if you understand the “why” (the four Docker gaps).
- Says you’ve learned an initial “5%” today; next is the remaining “95%” built on these foundations.
- Upcoming topics mentioned (in order he lists):
- Kubernetes architecture (next class)
- pods
- deployments
- services
- ingress controllers
- admission controllers
- Encourages persistence: you won’t fully grasp all components on day one, but understanding grows over time.
- Ends with like/comment/share.

