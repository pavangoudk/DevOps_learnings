# DevOps Day 31: Kubernetes Architecture (Control Plane vs Data Plane)

## Intro + warm-up: why “K8s”?

# 

- Speaker introduces **Day 31** and the topic: **Kubernetes architecture**.
- Starts with a light question: why Kubernetes is called **K8s** (invites viewers to answer in comments).
- Recommends watching **Day 30** first (Docker vs Kubernetes), because architecture won’t make sense without knowing why orchestration is needed.
- Recaps Day 30’s four Kubernetes advantages over Docker:
1. cluster behavior
2. auto-healing
3. auto-scaling
4. enterprise-grade support (advanced load balancing, security, advanced networking)

## Why he won’t teach architecture as “just a list of components”

# 
- Notes many videos/documentation explain Kubernetes with:
- control plane vs data plane
- and list components (API server, etcd, scheduler, controller manager, cloud controller manager; plus kubelet, kube-proxy, container runtime)
- Says if you only memorize “these components do these things,” you won’t truly understand the architecture.
- His approach: compare **Docker container creation** vs **Kubernetes pod creation**.
- *“In Docker the simplest thing is container whereas in Kubernetes the simplest thing is POD.”*
- Goal: by the end, understand why Kubernetes needs more components than Docker, and the “advantage of each and every component.”

## Container creation in Docker (what happens “under the hood”)

# 
- Setup example:
- a virtual machine
- Docker installed
- user runs `docker run`
- Key point: containers need a runtime; otherwise they cannot run.
- Example analogy: a Java app won’t run without Java runtime.
- Names the runtime component in Docker:
- **Docker shim** (as the container runtime component he mentions happening “under the hood”).

## Kubernetes setup: master + worker, requests go through control plane

# 
- Kubernetes is presented as:
- **one master** + **one worker** (for easy understanding)
- clarifies production usually has multiple workers and multiple masters, but demo explanation uses one of each.
- Important routing concept:
- you don’t send requests directly to workers; requests go through the **master/control plane**.

## Pod creation in Kubernetes: what runs on the worker node (data plane)

### ### Pod (vs container)

# 

- Pod is the smallest deployable unit in Kubernetes (he says he’ll explain pod vs container difference more tomorrow).
- For now: pod is like a wrapper around a container with “advanced capabilities.”

### ### kubelet

# 
- kubelet runs on the worker and is responsible for running/maintaining pods.
- It “always looks for” whether a pod is running.
- Because Kubernetes supports auto-healing, kubelet informs the control plane if something is wrong.

### ### Container runtime (Kubernetes runtime choices)

# 
- Kubernetes still needs a container runtime to run containers inside pods.
- Difference vs Docker:
- Docker has one runtime path (he references Docker shim).
- Kubernetes can use multiple runtimes:
- Docker shim
- containerd
- CRI-O (he says “Creo”)
- Kubernetes supports any runtime that implements Kubernetes’ standard interface:
- *“Kubernetes has a standard… container interface.”*
- If a runtime implements it, Kubernetes can use it.

### ### kube-proxy

# 
- kube-proxy provides networking-related capabilities:
- IP address allocation/handling for pods (as he frames it)
- default load balancing behavior needed for scaling replicas
- He ties it to auto-scaling:
- if replicas go from 1 to 2, there must be something that splits traffic (e.g., 50/50).
- Notes kube-proxy uses **iptables** on Linux for networking configuration (no deep dive).

### Worker node summary (explicitly stated)

# 
- If asked “worker node components,” he says you can answer:
1. **kube-proxy**
2. **kubelet**
3. **container runtime**
- Repeats purposes:
- kubelet: ensures pods are running; triggers action via control plane if not
- kube-proxy: networking + load balancing, uses iptables
- container runtime: runs containers

## Why control plane is needed (master components)

# 
- Even though worker components can run applications, Kubernetes needs control plane to handle enterprise/cluster-wide decisions, e.g.:
- where to place pods (node 1 vs node 2 vs node 3)
- handling many users/requests
- future needs like identity/SSO and security controls
- Names the “core” component that handles all incoming external requests:
- **API server**

### ### API server

# 
- *“Heart of Kubernetes”* (his phrasing later in the summary).
- Exposes Kubernetes to the external world and receives requests (e.g., create a pod).
- It decides “Node 1 is free” (decision-making input) and then other components act.

### ### kube-scheduler (scheduler)

# 
- Scheduler is responsible for scheduling pods/resources onto nodes.
- Speaker’s split of responsibilities:
- API server decides/coordinates
- scheduler “acts” by placing the pod on the chosen node

### ### etcd

# 
- etcd is Kubernetes’ backing store for cluster state.
- Definition given:
- *“etcd is basically a key value store.”*
- Stores “entire Kubernetes cluster information” as key-value objects/pairs.
- Without etcd, cluster information/restore capability is missing; backup is essential.

### ### controller manager

# 
- Kubernetes has multiple controllers (example: ReplicaSet controller) that maintain desired state.
- Controller manager is the component that ensures these controllers are running.
- *“Manager which is managing these controllers is called as a controller manager.”*
- He says deeper understanding will come later with deployments/services.

### ### Cloud controller manager (CCM)

# 
- Discusses Kubernetes on cloud providers:
- EKS, AKS, GKE (examples he names)
- Problem: Kubernetes must translate user requests (e.g., load balancer, storage) into cloud-provider-specific API calls.
- Role of CCM:
- bridges Kubernetes requests to the underlying cloud provider APIs.
- Extensibility idea:
- Kubernetes can’t embed logic for every cloud.
- CCM is open source; a new cloud provider could implement its logic and contribute.
- On-prem note:
- if running Kubernetes on-prem, CCM isn’t required / doesn’t need to be created.

## Full architecture recap: Control plane vs Data plane

# 
- Kubernetes is “divided into two parts”:

- **Control plane**
- **Data plane**
- Data plane (every worker node):

- kubelet
- kube-proxy
- container runtime (he notes some docs may omit it, but runtime is required)
- Control plane (master):

- API server (receives every request)
- scheduler (places workloads onto workers)
- etcd (cluster key-value datastore)
- controller manager (manages built-in controllers)
- cloud controller manager (cloud integration/translation)
- Interpretation he gives:

- control plane controls actions
- data plane executes actions

## Assignment + close

# 
- Assignment:
- write detailed notes on Kubernetes architecture
- post on LinkedIn
- include a diagram (e.g., drawn in Paint)
- also post notes/diagram on GitHub and share the URL on LinkedIn
- Invites questions in comments if any component isn’t clear.
- Next class preview: **Kubernetes Pod**.
