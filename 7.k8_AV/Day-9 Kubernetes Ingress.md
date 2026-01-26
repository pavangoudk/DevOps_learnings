# DevOps Day 38: Kubernetes Ingress (Why It Exists + Minikube Setup)

## Intro: why Ingress feels “tricky”

# - - - Speaker introduces **Day 38**: Kubernetes **Ingress**.
- Says people find Ingress difficult mainly because:
1. they don’t understand **why Ingress is required**
2. they struggle with **practical setup**, especially on **minikube/local clusters**
- Mentions he has an **end-to-end Ingress video** on the channel and will share it in the description.
- Recommends watching **Day 37 (Services deep dive)** first to understand Ingress better.
- Promises both **theory + practical** in this video.

## What is Ingress, and why do we need it if Services exist?

# - - - He restates what Services already provide:
- **service discovery**
- **load balancing** (shown via Kubeshark in Day 37)
- **exposing applications** externally (e.g., Service type LoadBalancer)
- Core question: If Services do all that, *why Ingress? what problem does it solve?*

### Historical context: Kubernetes before Ingress

# - - - Before Kubernetes v1.1 (around late 2015), **Ingress didn’t exist**.
- People used Kubernetes using only:
- Deployment → Pod
- Service on top (including LoadBalancer service type)

## The two practical problems that led to Ingress

### Problem 1: Services lack “enterprise / TLS” load balancing features

# - - - Users migrating from VMs/physical servers had mature load balancers such as:
- **Nginx**
- **F5**
- (later he also mentions **HAProxy**, **Traefik**, and others)
- “Enterprise load balancers” offered rich capabilities, e.g.:
- ratio-based load balancing (e.g., “3 requests to one, 7 to another”)
- *“sticky sessions”* (same user stays on same backend)
- path-based routing
- host/domain-based routing
- whitelisting / blacklisting by origin
- security features (mentions web application firewall)
- TLS/HTTPS-related configurations (adds security using TLS)
- But Kubernetes Service load balancing (via kube-proxy) was basically:
- *“a simple round robin load balancing”* (e.g., split 10 requests as 5/5 across two pods)
- His conclusion: Services didn’t provide the enterprise-grade feature set people were used to.

### Problem 2: LoadBalancer services become expensive at scale

# - - - Service type **LoadBalancer** provisions a **static public load balancer IP** on cloud.
- If you have hundreds/thousands of services (microservices), cloud providers charge heavily because:
- each LoadBalancer service can create its own load balancer / static public endpoint.
- Contrast with VM-era pattern:
- often **one** load balancer handled multiple apps using routing rules like:
- `/abc` → app1
- `/xyz` → app2
- one exposed IP instead of thousands.

He writes the two problems explicitly:

1. Missing **enterprise + TLS (secure) load balancing capabilities**
2. Cloud provider cost for **each** LoadBalancer service IP

> Interview emphasis: He calls these points “very important” to remember because interviewers ask: why Ingress? difference between LoadBalancer service and Ingress?

## Kubernetes’ solution: Ingress + Ingress Controller

# - - - Kubernetes acknowledged the problems.
- Mentions **OpenShift Routes** (Red Hat OpenShift) as a similar solution that existed.
- Kubernetes introduced:
- an **Ingress resource** (created by users)
- but Kubernetes cannot implement every load balancer’s logic itself.

### ### Ingress Resource

# - - - Kubernetes provides a resource you define in YAML to describe routing needs (examples he names):
- path-based routing
- host-based routing
- TLS-based ingress
- Key point:
- creating only the Ingress resource without a controller does nothing.

### ### Ingress Controller

# - - - *“Ingress controller is a load balancer that you can choose from the requirement.”*
- Vendors/projects implement controllers, e.g.:
- **Nginx Ingress controller**
- **F5 / BIG-IP controller**
- **HAProxy Ingress controller**
- mentions others like **Traefik**, **Ambassador**
- How the architecture works (his sequence):
1. Kubernetes user creates an **Ingress resource**
2. Organization installs an **Ingress controller** (often via **Helm charts** or YAML manifests)
3. Ingress controller **watches** Ingress resources and implements routing features (path/host/TLS, etc.)
- Notes:
- Ingress is not necessarily one-to-one with services:
- one Ingress can route to many services using different paths.

### Controller analogy to other Kubernetes components

# - - - He connects it to earlier concepts:
- for Pods, **kubelet** watches and manages pod lifecycle
- for Services, **kube-proxy** watches and updates iptables rules
- similarly, Ingress needs a component watching it → **Ingress controller**

## Practical section begins: current state check

### ### kubectl

# - - - He switches to terminal.
- Confirms he still has the resources from Day 37:
- `kubectl get pods` (deployment pods exist)
- `kubectl get service` (nodePort service exists)

### ### minikube

# - - - Verifies the service works by:
- getting `minikube ip`
- curling the NodePort endpoint and seeing the expected output.

## Create an Ingress resource (host-based routing example)

# - - - Goal for the demo:
- implement **host-based** routing.
- He contrasts:
- instead of accessing via IP:port, use a host like `example.com`.
- Uses Kubernetes docs:
- searches “kubernetes ingress”
- notes docs mention an “Ingress managed load balancer” (the controller).

### ### Ingress YAML (Ingress.yaml)

# - - - He chooses an example with a host and path.
- He creates `Ingress.yaml` and modifies fields:
- Ingress name: e.g., `ingress-example`
- host: uses `foo.bar.com`
- path: `/bar`
- backend service:
- service name: replaced with his service name (`python-django-app-service`)
- service port: `80`

### Apply and observe “nothing happens” (expected without controller)

# - - - Applies:
- `kubectl apply -f ingress.yaml`
- Checks:
- `kubectl get ingress`
- sees Ingress created but **ADDRESS is empty**
- Tries to curl `foo.bar.com/bar` and it fails.
- Explanation:
- Ingress controller is missing, so the Ingress resource isn’t acted upon.

## Install an Ingress controller (minikube path)

### ### Nginx Ingress Controller (minikube addon)

# - - - He decides to install **nginx ingress controller**.
- Notes Kubernetes supports many controllers (vendors implement them).
- For minikube, he uses the simple path:
- `minikube addons enable ingress`
- Mentions production approach:
- choose a controller and follow its official docs
- often install via **Helm** or YAML
- He gives an example of finding install steps by searching:
- “Ambassador ingress controller installation”
- then choosing Helm or kubectl YAML steps.

### Verify controller is running

### ### kubectl

# - - - Finds the ingress controller pod (not sure which namespace, so he searches across namespaces):
- `kubectl get pods -A | grep nginx`
- Observes:
- nginx ingress controller is running in namespace `ingress-nginx`.

### Check controller logs and confirm it saw the Ingress resource

# - - - Uses logs in the controller namespace:
- `kubectl logs -n ingress-nginx <controller-pod>`
- Sees it identified the Ingress resource `ingress-example` in the `default` namespace and “successfully synced.”
- He explains “synced” at a high level:
- controller updates nginx load balancer configuration based on the Ingress resource.

### ADDRESS field now populates

# - - - After installing the controller:
- `kubectl get ingress` now shows the ADDRESS populated (previously empty).

## Local-only requirement: map host to IP via /etc/hosts

# - - - He explains why this extra step is needed on local demo:
- `foo.bar.com` is not a real domain you own.
- In production you’d use a real domain (e.g., `amazon.com`) that resolves correctly.
- On local machine you must map:
- `foo.bar.com` → the Ingress IP (shown as something like `192.168.64.11` in his demo)

### ### /etc/hosts (domain mapping for local testing)

# - - - He edits `/etc/hosts` (using sudo) to add an entry mapping the host to the Ingress IP.
- Notes:
- if there are old entries, remove them to avoid conflicts.
- Alternative he mentions:
- instead of editing `/etc/hosts`, you can tell curl how to resolve the host to a specific IP for the request.

## Additional Ingress capabilities he points to (beyond the demo)

# - - - He references Kubernetes docs showing many Ingress examples:
- host + path routing (what he demoed)
- **TLS** / HTTPS Ingress (secure routing)
- Mentions his longer end-to-end video covers:
- TLS vs non-TLS
- host-based
- path-based
- wildcard
- SSL offloading / passthrough (named as examples)

## Close

# - - - Encourages viewers to explore the docs and watch the linked end-to-end video for full Ingress types and practicals.
- Ends with like/comment and “see you in the next video.”

