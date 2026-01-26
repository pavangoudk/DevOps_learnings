# DevOps Day 33: Deploy Your First Kubernetes App (Pod + kubectl + Minikube)

## Intro + prerequisites (why prior days matter)

# 

- Speaker introduces **Day 33**: deploying the **first application in Kubernetes**.
- Strong recommendation to watch:
- **Day 30** (Docker vs Kubernetes)
- **Day 31** (Kubernetes architecture)
- **Day 32** (Kubernetes installation / production systems)
- Repeats why Kubernetes is better than Docker (the reasons people move):
1. Kubernetes is a **cluster**
2. **auto-scaling**
3. **auto-healing**
4. **enterprise-level behavior**

## Core Kubernetes terminology needed before the demo

# 
- He doesn’t re-teach architecture here; instead, he introduces fundamentals needed to understand the deployment demo.

### ### Pod (the lowest level of deployment)

# 
- Key premise:
- *“In Kubernetes the lowest level of deployment is a pod.”*
- You **don’t directly deploy a container** in Kubernetes the way you do in Docker.
- He emphasizes that containers are still the unit running your application:
- end goal in both Docker and Kubernetes is to run applications in containers.
- Kubernetes says: deploy it “as a pod.”

### Pod definition (how he frames it)

# 
- *“A pod is described as… a definition of how to run a container.”*
- Docker comparison:
- In Docker, you run a container via command-line flags:
- `docker run ...`
- `-p` (port mapping)
- `-v` (volume mounts)
- `--network` (network details)
- etc.
- In Kubernetes, those “how to run it” details go into **`pod.yaml`**:
- *“Instead of command line you are trying to put everything in a yaml file.”*

### Why Kubernetes uses YAML (why this “complexity” exists)

# 
- Kubernetes aims for enterprise standardization and “declarative capabilities.”
- He stresses:
- Kubernetes is “all about YAML files” (pods, deployments, services, etc.).
- You don’t need to memorize YAML from scratch; engineers use official examples.
- But you **must understand how YAML is written** to become effective in Kubernetes.

## Pods can contain one or multiple containers (and why)

# 
- He states:
- *“A pod is nothing but one or group of containers.”*
- Typical case: **single container per pod**.
- Rare/advanced cases: multiple containers, e.g.:
- **sidecar containers**
- **init containers**
- Example scenario for multiple containers:
- one container needs config/user files from another container.
- instead of two separate pods, put both containers into one pod.

### Advantages of multiple containers in one pod

# 
- If containers A and B are in the same pod, Kubernetes enables:
- **shared networking**
- **shared storage**
- Networking implication:
- containers in the same pod can talk via **localhost**
- example: container can access the other on `localhost:3000`.

### Pod networking detail

# 
- Kubernetes assigns an IP to the **pod**, not individual containers:
- *“IP addresses are not generated for the containers but they are generated for the pods.”*
- Access model he describes:
- Kubernetes allocates a **cluster IP address** to the pod, and you access the containerized app via that pod IP.
- He links this back to Day 31:
- kube-proxy is the component generating/providing cluster networking behavior.

## Why pods help DevOps teams (practical motivation)

# 
- He frames pod as a wrapper that makes ops easier at scale:
- with hundreds/thousands of containers, a `pod.yaml` in Git makes it easy to see:
- what port the app uses
- whether it has volume mounts
- networking details
- other runtime settings

## ### kubectl (Kubernetes CLI)

# 
- Definition:
- *“kubectl is command line for Kubernetes.”*
- Docker analogy:
- Docker has docker CLI; Kubernetes has kubectl to interact with clusters.

Examples he gives (conceptually, then later in demo):

- `kubectl get nodes`
- `kubectl get pods`
- `kubectl get deployment`
- create/delete resources

## Plan for today’s demo (what he will do)

# 
1. Install **kubectl**
2. Create a local Kubernetes cluster using **minikube**
3. Deploy the first application as a **pod**

He chooses minikube because:

- kOps/EKS cost money unless you have credits
- local clusters are fine for learning (not production-grade, not HA)
- many viewers already use minikube

## Demo: install tools, start cluster, deploy first pod

### ### kubectl installation

# 

- Instructional approach:
- search “kubectl installation”
- go to Kubernetes “Install tools” page
- choose OS (Linux/Mac/Windows)
- on Mac: choose Intel vs Apple Silicon (M1/M2/ARM)
- copy/run the script
- Verifies installation with:
- `kubectl version`

### ### Minikube installation

# 
- He mentions alternatives (minikube, k3s, kind, microk8s), but uses **minikube** for the course demos.
- Notes (extra context):
- he personally prefers **kind** for practice because it’s “Kubernetes in Docker” and can run many clusters locally.
- Installation approach:
- search “minikube”
- pick OS and architecture (x86_64 vs arm64)
- run the provided commands
- Verifies installation by checking `minikube` is available.

### ### Start the Kubernetes cluster (minikube start)

# 
- Key concept:
- Minikube is installed, but cluster isn’t created until you run:
- `minikube start`
- How minikube works on Mac/Windows (his explanation):
- creates a **virtual machine**
- runs a **single-node Kubernetes cluster** on it
- Mentions driver options:
- default can use Docker driver
- for more advanced work, he suggests using:
- `--driver=hyperkit` (on Mac)
- (also mentions VirtualBox as an option)

### Verify cluster connectivity

# 
- Uses:
- `kubectl get nodes`
- Notes:
- shows one node (minikube)
- in single-node setup, that node is both **control plane and data plane**

## Deploy the first application as a Pod

### ### Pod YAML (Kubernetes documentation example)

# 

- He directs to Kubernetes docs for pod examples.
- Advises:
- copy the sample YAML; don’t memorize it.
- YAML keys stay largely the same; values change (name, image, ports, etc.).
- Uses example image:
- `nginx:1.14.2`
- Port note:
- sample uses containerPort 80
- if your app uses 8000/9000, update containerPort accordingly

### Docker equivalence (to help Docker learners)

# 
- He maps the pod YAML to a rough Docker run equivalent:
- run in background (`-d`)
- set name
- map port `80:80`
- image nginx
- Purpose: reinforce that a pod YAML is a “specification of how to run your container.”

### Create the pod

# 
- Uses kubectl to apply the YAML:
- `kubectl create -f pod.yaml`

### Check pod status

# 
- Uses:
- `kubectl get pods`
- `kubectl get pods -o wide` (shows more detail including pod IP)

### Access the application (from inside the cluster)

# 
- Since it’s not exposed externally, he SSHs into minikube:
- `minikube ssh`
- From inside the node, he curls the pod IP and sees the nginx page (thank-you message).
- Notes for real clusters:
- you would SSH into a master/worker node and test similarly.

## ### kubectl cheat sheet (how to learn commands)

# 
- He recommends using the official **kubectl cheat sheet**:
- search “kubectl cheat sheet”
- use it to find command variants (pods across namespaces, descriptions, etc.)
- He says even experienced users reference it.

## Deleting the pod

# 
- Demonstrates lifecycle control:
- `kubectl delete pod <pod-name>`

## What’s next: auto-healing and auto-scaling (why pods aren’t the end)

# 
- He highlights the natural next question:
- how to get **auto-healing** and **auto-scaling** for the application
- Answer:
- use **Deployment** as a wrapper over Pods:
- *“On top of the pod you have a wrapper called deployment in Kubernetes.”*
- Deployment is the typical real-world way to deploy apps; pods alone aren’t used for production deployments.
- He previews tomorrow:
- deployment YAML looks similar to pod YAML
- main change is `kind: Deployment` plus additional fields like template.

## Debugging pods (basic troubleshooting commands)

### ### kubectl logs

# 

- Command:
- `kubectl logs <pod-name>`
- Purpose:
- view application logs (he notes the demo nginx may not emit meaningful logs until you generate traffic)

### ### kubectl describe

# 
- Command:
- `kubectl describe pod <pod-name>`
- Purpose:
- prints detailed pod status and events
- he positions this as an interview-ready answer for “how do you debug a pod?”

## Close: practice warning

# 
- He urges viewers to practice today’s steps because complexity will increase (deployments, services, auto-healing, auto-scaling).
- Ends with like/share and next video teaser.

