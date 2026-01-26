# DevOps Day 35: Kubernetes Services (Why They Matter + Service Types)

## Intro: services are “critical” in Kubernetes

# 

- Speaker introduces **Day 35**: Kubernetes **Services**.
- Recap from Day 34:
- In production you usually deploy a **Deployment**, not a raw Pod.
- New premise:
- For each deployment, “most of the times” you also create a **Service**.
- Sets today’s goal: understand the **“why”** of services and what breaks if services don’t exist.

## “What if Kubernetes had no Services?” (problem setup)

# 
- Assumption: there is **no concept of Services** in Kubernetes.
- Typical flow still exists:
- DevOps/developer creates a **Deployment**
- Deployment creates a **ReplicaSet**
- ReplicaSet creates **Pods**
- Example: requirement is **3 replicas** (pod1, pod2, pod3).

### Why multiple pod replicas are needed (traffic example)

# 
- If only 1 user, you may not need replicas.
- For many concurrent users (he defines “concurrent” as people using at the same time—WhatsApp example):
- if all requests hit one pod, it can go down due to load.
- He gives a sizing heuristic:
- ideal pod count depends on:
- number of concurrent users/requests
- how many requests one replica can handle
- example: if one replica handles 10 requests at a time and you have 100 requests, you need 10 pods.
- Notes this is a joint decision between developers and DevOps.

## Auto-healing creates a new problem: changing pod IPs

# 
- A pod going down is common in containers/Kubernetes.
- Kubernetes advantage: **auto-healing** (via Deployment/ReplicaSet) recreates pods.
- Scenario:
- initial pod IPs might be like:
- 172.16.3.4, 172.16.3.5, 172.16.3.6
- when a pod is recreated, its IP changes (e.g., 172.16.3.4 → 172.16.3.8)
- Without services, he describes a broken workflow:
- DevOps shares individual pod IPs with test teams / other dependent projects.
- Those teams keep using the old IP, then report “application not reachable.”
- Neither side is “wrong”; the IP simply changed due to auto-healing.

### Real-world analogy: Google doesn’t give per-user IPs

# 
- Google would never tell users:
- “User group A hit this IP; user group B hit that IP.”
- Instead, real systems rely on **load balancing** and stable access points.

## Solution: create a Service on top of the Deployment

### ### Service (SVC)

# 

- He introduces that on top of the deployment you create a **Service** (shortcut: **SVC**).
- What the service provides (first key benefit):
- **Load balancing**
- He notes (without diving deep):
- service uses a Kubernetes component called **kube-proxy** to do this (asks viewers to ignore kube-proxy details for now).

### Example service name / DNS style

# 
- He gives a service-style name like:
- `payment.default.svc`
- (service name + namespace + `.svc`)
- Idea: users/projects access the application via the **service**, not individual pod IPs.
- Service spreads incoming requests across pods (e.g., split 10 requests to each replica).

**Takeaway so far:** Services prevent failures caused by changing pod IPs by providing a stable load-balanced front door.

## Second problem services solve: Service Discovery (labels + selectors)

# 
- He anticipates a follow-up question:
- “If pod IPs change, won’t the service also fail when forwarding traffic to old IPs?”
- Answer: service doesn’t “track IPs manually.”
- Second key benefit:
- **Service Discovery**

### ### Labels and Selectors

# 
- Mechanism introduced:
- services discover pods via **labels and selectors** instead of hardcoded IPs.
- Process he describes:
1. DevOps/devs apply a common label to pods (e.g., `payment`)
2. Service “watches” pods with that label
3. When pods are recreated (new IP) or scaled (new replicas), labels remain consistent because ReplicaSet recreates pods from the same YAML spec
4. Service continues routing correctly without caring about IP churn

He summarizes why this is “advanced”:

- Kubernetes service discovery is strong because it relies on **labels/selectors**, not IP lists (especially important if there are tens/hundreds/thousands of pods).

## Label flow diagram (as he explains it)

# 
- You create a **Deployment** via YAML.
- In deployment metadata you add a label (he frames label as “just a tag”), example:
- `app: payment`
- Deployment → ReplicaSet → Pods
- Each pod gets the same label (`app: payment`).
- Even if a pod dies and comes back with a new IP, the label stays the same, so the service continues to discover it.

## Third benefit: exposing applications outside the cluster

# 
- He transitions to the third capability:
- **Expose the application to the world**
- Problem statement:
- Without exposure, only someone who can SSH into nodes / access cluster network can hit pod IPs.
- Real users (e.g., “in Italy” or “Austria”) won’t SSH/VPN into your cluster to use the app.
- Real apps are accessed like `https://google.com`.

### Service “type” options (3 defaults)

# 
- In a Service YAML, you choose a type:
1. **ClusterIP**
2. **NodePort**
3. **LoadBalancer**

- (He mentions other types exist like headless, but not covering them.)

#### ClusterIP (default)

# 
- If service type is **ClusterIP**:
- app is accessible **only inside the Kubernetes cluster**
- still provides the two internal benefits:
- load balancing
- service discovery

#### NodePort

# 
- If service type is **NodePort**:
- app is accessible to people who can reach the **worker node IP addresses**
- he frames this as: anyone with access to the organization/network/VPC that can reach node IPs

#### LoadBalancer

# 
- If service type is **LoadBalancer** (cloud-specific behavior):
- exposes the app to the **external world**
- on cloud (e.g., EKS), Kubernetes creates a cloud load balancer and returns a **public IP**
- He notes:
- LoadBalancer type generally works on **cloud providers**
- It won’t work “by default” on minikube/local clusters; a workaround will come later via **Ingress**.

### ### Cloud Controller Manager (connection to Day 31)

# 
- For LoadBalancer services on cloud, he ties it to:
- **Cloud Controller Manager**
- It helps generate/provision the cloud load balancer IP via the cloud provider implementation (AWS example).

## One consolidated diagram (user access by service type)

# 
- ClusterIP:
- public users cannot reach it
- only cluster-network-access users can
- LoadBalancer:
- Kubernetes (via cloud provider) provides a public load balancer IP
- anyone on the internet can access
- NodePort:
- accessible to anyone who can reach the worker node/EC2 IPs (e.g., within the VPC / organization network)

He adds a stricter ClusterIP note:

- even if you can access the VPC/EC2, ClusterIP isn’t for you unless you can access the **Kubernetes cluster network** (mentions CNI examples like flannel/calico).

## Recap: the three advantages of Kubernetes Services

# 
1. **Load balancing**
2. **Service discovery** (via **labels and selectors**)
3. **Exposing applications** (ClusterIP vs NodePort vs LoadBalancer)

## Practical selection guidance (Amazon example)

# 
- Example framing:
- public app like `amazon.com` → choose **LoadBalancer**
- internal-only (people with VPC/node access) → choose **NodePort**
- only cluster-access users (DevOps / cluster network) → choose **ClusterIP**

## Assignment + close

# 
- Assignment:
- write a few lines + draw a diagram explaining the three service types
- post to LinkedIn/GitHub to validate your understanding
- Ends with like/comment/subscribe/share and “see you in the next video.”

