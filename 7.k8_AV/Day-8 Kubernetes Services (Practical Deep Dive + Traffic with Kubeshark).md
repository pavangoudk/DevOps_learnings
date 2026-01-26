# DevOps Day 37: Kubernetes Services (Practical Deep Dive + Traffic with Kubeshark)

## What this class covers (practical focus)

# 

- Speaker introduces **Day 37**: a **deep dive into Kubernetes Services** with a hands-on session.
- Goal: demonstrate, practically, the three service aspects discussed earlier:
1. **load balancing**
2. **service discovery**
3. **exposing applications** (outside the world / within the cluster / within the organization)
- He strongly recommends watching till the end because he will use **Kubeshark** to visualize **how traffic flows inside Kubernetes** and how components communicate.

## ### Minikube (demo cluster setup)

# 
- He uses a **minikube Kubernetes cluster** for the demo.
- Verifies it’s running with:
- `minikube status`
- Notes:
- If you don’t know how to create clusters, refer to earlier videos:
- local cluster via minikube
- AWS cluster via **kOps** (requires credits/resources)

## Clean slate: remove existing resources

### ### kubectl

# 

- He clears existing resources in the default namespace:
- `kubectl get all` (to inspect current resources)
- deletes an existing deployment:
- `kubectl delete deploy <name>`
- deletes an existing service:
- `kubectl delete svc <name>`
- Mentions:
- you won’t remove the default Kubernetes service (`kubernetes`).
- After cleanup:
- `kubectl get all` should show only the default Kubernetes service.

## Demo app source: reuse existing repo and images

### ### “Docker Zero to Hero” GitHub repository (demo app source)

# 

- He uses his repo (previously used in earlier classes), containing practical:
- **Python** and **Golang** apps (front-end/back-end style examples)
- For today’s demo, he chooses the **Python** app.
- Repo contents he points out:
- application code folder (devops folder)
- `Dockerfile`
- `requirements.txt`

## Build the container image (from scratch)

### ### Docker (build)

# 

- He shows the app’s Dockerfile is a simple **Python Django** app.
- Builds the image:
- `docker build -t python-sample-application-demo:v1 .`
- Notes:
- app has an entrypoint/CMD so it self-executes when run (no extra args needed).

## Create a Deployment for the app

### ### Kubernetes Deployment (deployment.yaml)

# 

- He does **not** write syntax from memory:
- searches “kubernetes deployment”
- copies the example from Kubernetes docs into `deployment.yaml`
- Edits required fields:
- replicas: chooses **2** (to show load balancing)
- deployment name: `sample-python-app` (his chosen name)
- labels: emphasizes labels are important on every resource
- selector matchLabels: must match pod template labels
- pod template labels: uses same `app: sample-python-application`
- container image: replaces with the image he built (`python-sample-application-demo:v1`)
- container port: sets to **8000** (because app runs on 8000)

### How he determines the application port

# 
- Says you can check:
- Dockerfile `EXPOSE`, or
- startup command details
- DevOps/devs should know which port the application runs on.

### Apply and verify the deployment

# 
- Creates deployment:
- `kubectl apply -f deployment.yaml`
- Checks deployment status:
- `kubectl get deploy`
- Checks pods created:
- `kubectl get pods`

## Inspect pod IPs + show why IPs are unstable

### ### kubectl get pods -o wide

# 

- Uses:
- `kubectl get pods -o wide` to see pod IP addresses.

### ### kubectl verbosity (behind-the-scenes API calls)

# 
- If curious how kubectl works, he demonstrates verbosity:
- `kubectl get pods -v=7` (example)
- Explains output shows:
- kubeconfig file loaded
- connection to API server
- API request made
- response status (e.g., 200)
- Notes `-v=9` gives even more detail (request/response payloads).

### Pod deletion shows IP change

# 
- Deletes one pod to trigger ReplicaSet recreation (concept from prior class):
- `kubectl delete pod <pod-name>`
- After recreation, `kubectl get pods -o wide` shows IPs changed (example: `.6` replaced by `.7`).
- He restates the problem:
- dynamic IP allocation means clients using old pod IP will experience traffic loss.
- Connects back to yesterday’s theory:
- Services solve this via **labels and selectors**, not IP tracking.

## Accessing the app without a service (cluster-only)

### ### minikube ssh (access within the cluster)

# 

- Demonstrates that pod IPs are reachable only from inside the cluster network:
- `minikube ssh`
- `curl -L http://<pod-ip>:8000/demo`
- Notes:
- App requires a redirect, so he uses `-L`.
- The app’s context root is `/demo`.
- He validates `/demo` by pointing to Django `urls.py`.

### Key observation

# 
- Accessing pod IP directly is **inside-cluster** behavior; customers won’t SSH into your cluster.

## Reframe access needs: internal org vs external customers

# 
- He draws a simple mental model:
- Kubernetes cluster sits inside an organization network.
- Two user categories:
1. **inside organization** (can reach node IPs)
2. **outside organization** (needs a public endpoint)
- Maps to service types:
- internal org access → **NodePort**
- external world access → **LoadBalancer**

## Create a Service (NodePort) and test access

### ### Kubernetes Service (service.yaml) — NodePort

# 

- Again, he doesn’t memorize syntax:
- searches Kubernetes docs for service examples
- chooses **NodePort** example
- Creates `service.yaml` and edits key fields:
- `metadata.name`: e.g., `python-django-app-service`
- `spec.selector`: must match the **pod template label** from the deployment (`app: sample-python-application`)
- `nodePort`: keeps example value (e.g., `30007`)
- `targetPort`: sets to **8000** (app port)
- Applies service:
- `kubectl apply -f service.yaml`
- Verifies:
- `kubectl get svc`
- He notes:
- even for NodePort services, a **ClusterIP** is still assigned (don’t be confused).
- NodePort adds a port mapping (ClusterIP/service port → NodePort).

### Test via ClusterIP (from inside cluster)

# 
- Using `minikube ssh`, you can curl service ClusterIP:
- `curl -L http://<service-cluster-ip>:80/demo`
- He says this isn’t the main point because you could already curl pod IPs internally.

### Test via NodePort (from laptop, no SSH)

# 
- Gets node IP:
- `minikube ip`
- Accesses from local machine:
- `http://<minikube-ip>:30007/demo`
- Shows it works:
- via curl and via browser.
- Important caveat:
- other people “outside” won’t be able to access this NodePort endpoint in the general case; NodePort is for those who can reach node IPs (org/VPC access).

## Switch service type to LoadBalancer (conceptual on minikube)

### ### kubectl edit svc

# 

- Edits service:
- `kubectl edit svc <service-name>`
- Changes:
- `type: NodePort` → `type: LoadBalancer`
- On minikube:
- `kubectl get svc` shows **EXTERNAL-IP pending** (because LoadBalancer is cloud-provider behavior).
- He explains:
- On AWS/Azure/GCP, you’d get an external IP.
- The **Cloud Controller Manager** generates it, based on cloud-provider contributions.

### ### MetalLB (mentioned workaround for local clusters)

# 
- Mentions a project **MetalLB** that can provide LoadBalancer-like behavior on minikube/local clusters.
- He calls it “still a beta project” and says understanding the concept is enough.

## Service discovery demo: break selector, observe failure

### ### Labels & selectors (service discovery proof)

# 

- Goal: show service discovery depends on selector matching pod labels.
- Method:
- modify the service selector so it no longer matches pod labels (remove/change one character).
- re-apply:
- `kubectl apply -f service.yaml`
- Test:
- trying the same browser/URL fails (“couldn’t connect”).
- Fix:
- restore the correct selector and re-apply.
- Notes:
- give it a short moment because **kube-proxy** needs to update rules/iptables.

## Load balancing demo with traffic visualization

### ### Kubeshark (traffic viewer)

# 

- Introduces Kubeshark as a practical tool to see:
- how traffic flows within Kubernetes
- how components talk to each other
- Installation guidance (as he states):
- follow Kubeshark docs; install is a simple curl command (or two commands on Mac).
- Run:
- `kubeshark tap -A` (tap all namespaces)
- opens UI at `localhost:8899`

### Use case: prove service load-balances across pods

# 
- He sends multiple requests (six) to the same NodePort URL.
- Expected behavior:
- service should distribute requests (round-robin) across the two pod IPs (e.g., `172.17.0.5` and `172.17.0.7`).

### Kubeshark troubleshooting moment (what happened)

# 
- He encounters an error:
- “error while proxying the request / context canceled”
- Fix he mentions:
- re-establish connection using a “kubeshark proxy” command (he doesn’t go deep into details here).
- then continues the demo.

### Observed result in Kubeshark

# 
- Requests alternate between the two pod IPs, confirming load balancing:
- one request to `.5`, next to `.7`, and so on.
- He points out:
- user hits the service endpoint (Node IP + NodePort)
- service forwards to different pods.

## Packet flow explanation (high-level, as shown via Kubeshark)

# 
- He explains “source” vs path:
- source/origin is his laptop (browser/curl).
- he checks local IP with `ifconfig` (example shown as `192.168.64.1`).
- Example path he narrates:
- request from laptop → minikube/node IP → service → one of the pods
- response returns back along the path.
- Mentions you can further analyze traffic with:
- **Wireshark**
- **tcpdump**
- Says he’ll cover Kubeshark deeply in a dedicated video, including:
- service maps (how services talk)
- pod lists per namespace
- TCP/HTTP, layer 4/layer 7 views

## Wrap-up: what was proven in this practical

# 
- He reiterates the three service capabilities demonstrated:

1. **Exposing the application** (NodePort; LoadBalancer concept)
2. **Service discovery** (labels/selectors; mismatch breaks routing)
3. **Load balancing** (verified via Kubeshark traffic alternating across pods)
- Closes with like/comment/share and promises a dedicated Kubeshark video.

# - -

