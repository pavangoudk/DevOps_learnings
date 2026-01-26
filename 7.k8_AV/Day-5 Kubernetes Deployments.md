# DevOps Day 34: Kubernetes Deployments (Pods vs Deployments vs ReplicaSets)

## Intro + context (where this fits in the course)

# 

- Speaker opens **Day 34** of the 45-day DevOps journey.
- Topic: **Kubernetes Deployment**.
- Recap of Days **30–33**: Kubernetes vs Docker, Kubernetes architecture, Kubernetes installations, and Pods.
- Emphasizes Day 33 (Pods) is required to understand deployments.

## Core interview framing: container vs pod vs deployment

# 
- He calls this an interview question: *difference between a container, a pod, and a deployment*.

### Container (Docker baseline)

# 
- Container platforms (example: **Docker**) run containers using CLI flags:
- `docker run -it` (or `-d` for detached)
- image name
- `-p` for ports
- `-v` for volumes
- `--network` for networking
- (He notes he’s not going into Dockerfile → image → container workflow here.)

### Pod (Kubernetes “run specification”)

# 
- Kubernetes changes the model:
- instead of passing runtime parameters on the command line, you define them in a **YAML manifest**.
- Definition-style statement:
- *“A pod.yaml… is nothing but a running specification of your Docker container.”*
- Pod can be:
- single container, or
- multiple containers (e.g., sidecar patterns)

#### Example use case he gives for multiple containers

# 
- **Service mesh** style pattern:
- one “sidecar container” + one “actual container”
- Benefit:
- share the same networking (communicate via `localhost`)
- share storage/volumes

## Why deployments exist (why pods are not enough)

# 
- Key question he raises: if you can deploy an app as a pod, why do you need a deployment?
- Answer: pods do **not** provide Kubernetes’ core platform features:
- **auto-healing**
- **auto-scaling**
- He summarizes:
- Pod is “similar to your container” in that it’s mainly a YAML spec to run containers (plus optional multi-container advantages).
- To get enterprise behaviors like zero downtime + auto healing/scaling, you use **Deployment**.

## Deployment mechanics (Deployment → ReplicaSet → Pod)

# 
- He describes the flow:
1. You create a **Deployment** resource
2. It creates an intermediate resource: **ReplicaSet**
3. ReplicaSet creates the **Pods**
- He says: “don’t create pods directly”; you will end up with pods anyway, but you create them through Deployment.

### ### ReplicaSet (RS)

# 
- ReplicaSet is described as:
- *“a Kubernetes controller”* responsible for maintaining pod replicas.
- What you set in a deployment:
- number of replicas (e.g., 2, 100, 150)
- Auto-healing behavior explanation:
- if the desired replica count is 100 and someone deletes a pod (or a pod dies), ReplicaSet brings it back to 100.
- Scaling explanation:
- if you change replicas from 100 → 150, ReplicaSet creates 50 more pods.

### ### Kubernetes Controller (concept definition)

# 
- He defines controller behavior:
- *Controllers “maintain a state”… ensuring “desired state” equals “actual state” on the cluster.*
- Notes:
- Kubernetes has default controllers.
- Also mentions custom controllers exist (examples he names): **Argo CD**, **admission controllers**.

## Interview questions he highlights

# 
1. Difference between **container vs pod vs deployment**
2. Difference between **deployment vs ReplicaSet**
- ReplicaSet is the controller implementing auto-healing by keeping actual state aligned to desired replicas.
- Deployment creation automatically creates a ReplicaSet.

## Live demo (kubectl): pods vs deployments in practice

### ### kubectl get all (namespace inventory)

# 

- He clears the environment (deletes an existing deployment).
- Shows:
- `kubectl get pods` → none
- `kubectl get deploy` → none
- Mentions:
- `kubectl get all` lists resources in the current namespace.
- `kubectl get all -A` lists resources across all namespaces.
- Calls this an interview-ready answer for listing resources.

### Pod demo recap (why pod-only is fragile)

# 
- Uses the same `pod.yaml` from the prior class (nginx example).
- Creates the pod:
- `kubectl apply -f pod.yaml`
- Validates:
- `kubectl get pods`
- `kubectl get pods -o wide` to see the IP
- Access pattern:
- minikube: `minikube ssh` then `curl <pod-ip>`
- remote cluster: SSH into a node using identity file and node IP
- Demonstrates the problem:
- deletes the pod (`kubectl delete pod nginx`)
- after deletion, the app is not reachable because the pod is gone
- Transition message:
- Kubernetes supports auto-healing/scaling, but only if you create the **correct resource** (Deployment, not a raw Pod).

### Deployment demo (how it creates RS + pods)

# 
- He says don’t memorize YAML syntax; use Kubernetes docs/examples and modify needed fields (image, etc.).
- Applies `deployment.yaml`:
- `kubectl apply -f deployment.yaml`
- Verifies the chain:
- `kubectl get deploy` → deployment exists
- `kubectl get rs` → ReplicaSet exists (RS shortcut)
- `kubectl get pods` → pod(s) exist
- Key point:
- Deployment is a high-level abstraction; ReplicaSet controller does the state enforcement.

### Auto-healing demonstration (watch mode)

# 
- Sets up watch:
- `kubectl get pods -w` (live watch)
- Deletes a pod while watching.
- Observed behavior he highlights:
- pod enters **Terminating**
- **a new pod starts Running in parallel**
- termination and creation happen simultaneously
- Interpretation:
- ReplicaSet ensures the desired replica count (e.g., 1) is always met, even if someone deletes a pod.

### Scaling demonstration (replicas 1 → 3)

# 
- Edits `deployment.yaml` (Vim) and increases replica count to **3**.
- Re-applies:
- `kubectl apply -f deployment.yaml`
- Verifies:
- `kubectl get pods` shows **3 pods**
- Deletes one of the pods:
- ReplicaSet immediately creates a replacement so **3 remain running**.

## Real-world takeaway + assignment

# 
- Production practice:
- *“In production scenarios you will never create a pod directly”*; you create **Deployments** (and later: StatefulSets/DaemonSets).
- Assignment:
- create a deployment
- replace the image
- “play with” Kubernetes:
- delete pods and observe auto-healing
- increase replicas and observe scaling behavior
- check ReplicaSets with `kubectl get rs`

## Examples to practice with (search + GitHub)

# 
- Suggests finding deployment examples online (search “kubernetes deployment examples” + GitHub).
- Points to Kubernetes’ official examples repo and mentions **guestbook** and “all-in-one.yaml” as an example containing a deployment.

## Zero downtime concept (as he describes it)

# 
- He calls out “zero time/zero downtime deployment” behavior:
- scaling from 1 → 3 didn’t disturb existing pods
- deleting a pod didn’t disturb the running app because replacement happens in parallel
- Notes future topics (not covered yet):
- services and ingress play a role in user-facing traffic, coming later.

