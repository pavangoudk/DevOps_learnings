# DevOps Day 42: Kubernetes Monitoring with Prometheus + Grafana (Minikube Demo)

## Intro + what’s provided

# - - - Speaker introduces **Day 42**: Kubernetes **monitoring**.
- Says it’s not just theory:
- he created a **GitHub repository** with practical installation steps and commands used in the demo.
- plans to expand it later (mentions: “advanced Kubernetes monitoring” and “writing your own metric server”).
- suggests starring the repo for future updates.
- What will be covered (in order):
1. “Why” monitoring: advantages, why needed
2. What is **Prometheus**, what is **Grafana**
3. Installation (only prerequisite: any Kubernetes cluster—minikube, k3s, k3d, production, etc.)
4. Demo: monitor **minikube** with Prometheus and visualize in Grafana
- build a Grafana dashboard showing metrics for API server and deployments (replica/status info)

## Why monitoring is required (organization scaling example)

# - - - With **one** Kubernetes cluster:
- a single DevOps engineer can manually monitor it.
- When **multiple teams** share a cluster:
- teams might report issues like:
- deployment not receiving requests
- service not accessible briefly
- DevOps needs visibility to understand what’s happening.
- As environments grow (Dev / Staging / Prod):
- number of clusters grows → monitoring becomes necessary at scale.

## ### Prometheus

# - - - He says Prometheus was initially developed by **SoundCloud** and is now open source (anyone can use it).
- Prometheus role:
- collect metrics from the cluster, store them, and enable querying/alerting.

### Prometheus architecture (high-level)

# - - - Components/flow he explains:
- **Prometheus server** (has an HTTP server)
- Kubernetes **API server** exposes metrics:
- if you access the API server `/metrics`, you can get status info for cluster resources.
- Prometheus fetches these metrics and stores them in a **time series database**.
- Definition-style phrasing:
- *A time series database stores metrics “with respect to the timestamp.”*
- Storage:
- Prometheus stores data on disk (HDD/SSD).
- Alerting path:
- Prometheus can push alerts to **Alertmanager**.
- Alertmanager can route notifications to places like:
- Slack
- Email
- “Google Meet” (mentioned as an example of destinations)
- Querying/consumption:
- Prometheus has a UI where you can run **PromQL** queries.
- Prometheus also supports an API (he mentions using curl/Postman).

## ### Grafana

# - - - Grafana purpose:
- visualization of metrics.
- He contrasts outputs:
- Prometheus can return results (often seen as structured output like JSON), which is hard to read at scale.
- Grafana displays the same data as charts/diagrams for easier dashboards.
- Key point:
- Grafana can use multiple data sources; **Prometheus can be one data source**.

## Demo setup: create a Kubernetes cluster (minikube)

### ### Minikube

# - - - He creates the cluster “from scratch” to show steps again.
- Notes:
- He often uses **kind** for local dev because it’s lightweight (“Kubernetes in Docker”).
- For demos needing more CPU/memory, he prefers **minikube**.
- Starts minikube with:
- `minikube start --memory=4g --driver=hyperkit`
- Explains driver choice:
- without specifying, minikube may use Docker driver (Docker Desktop).
- for “better/easier networking configurations,” use virtualization (he prefers hyperkit on Mac; VirtualBox also possible).
- Verifies cluster components:
- `kubectl get pods -A`
- sees default control-plane components (API server, controller manager, CoreDNS, etcd).

## Install Prometheus (Helm approach)

### ### Helm

# - - - He says install options include **Helm** or **operators**.
- Notes on operators (general point):
- operators can manage lifecycle/automatic upgrades; he’ll cover operators later.
- Chooses Helm for this class.
- Steps (copied from his GitHub repo):
1. `helm repo add prometheus-community ...`
2. `helm repo update` (he stresses doing this to avoid installing old versions)
3. `helm install prometheus prometheus-community/...`
- He advises reading Helm install output because it includes important access/URL/port-forwarding info.

### Verify Prometheus install

### ### kubectl

# - - - `kubectl get pods` shows Prometheus-related pods:
- Prometheus server
- **kube-state-metrics** (he highlights this component)
- `kubectl get svc` shows services created (ClusterIP):
- Prometheus server
- kube-state-metrics service
- Alertmanager

## What kube-state-metrics is and why it matters

### ### kube-state-metrics

# - - - He explains Kubernetes API server exposes many metrics “out of the box,” especially in newer/mature setups.
- But for deeper cluster object visibility (beyond API server basics), you may want:
- deployments/pods/services info
- whether deployment replica counts match expected
- kube-state-metrics provides “a lot of information” beyond default API server metrics by exposing its own `/metrics` endpoint.
- He notes when installed via Helm, kube-state-metrics is installed by default.

## Expose Prometheus UI (NodePort)

### ### kubectl expose (service)

# - - - Prometheus server service is ClusterIP; he wants to access the UI externally in the demo.
- Uses a provided command to expose it (creates a NodePort service named like `prometheus-server-ext`).
- Then:
- `kubectl get svc` shows the new NodePort (he cites port `31110` in the walkthrough).
- `minikube ip` gives node IP.
- Opens Prometheus UI in browser: `http://<minikube-ip>:31110`
- He notes:
- you can run PromQL queries in the UI.
- you can learn queries from docs or ChatGPT (as he says).

### Note on custom application metrics (future-looking in the talk)

# - - - He explains default metrics may not include deep app-level metrics.
- For app-specific health/behavior metrics:
- developers should expose a `/metrics` endpoint using Prometheus metrics libraries.
- Prometheus can scrape that endpoint via config.

## Install Grafana (Helm approach)

### ### Helm

# - - - Uses his repo’s Grafana folder/Helm instructions:
1. Add Grafana Helm repo
2. `helm repo update`
3. `helm install grafana ...`
- He highlights an important step:
- retrieve Grafana admin password from a Kubernetes secret (command shown in his repo).

### Expose Grafana UI (NodePort)

### ### kubectl expose (service)

# - - - Grafana service is ClusterIP; he exposes it to NodePort (creates `grafana-ext`).
- Notes:
- in real environments you’d use Ingress/Ingress controller (or operators might create routes automatically).
- Uses:
- `kubectl get svc` to find NodePort (he cites `31281`).
- `minikube ip`
- Opens Grafana: `http://<minikube-ip>:31281`
- Logs in:
- username `admin`
- password from the secret command.

## Connect Grafana to Prometheus (data source)

# - - - In Grafana:
- goes to **Data sources** → “Add your first data source”
- selects **Prometheus**
- enters the Prometheus URL (the service endpoint he used)
- clicks **Save & Test** to confirm it works
- Result:
- Grafana can now query Prometheus for metrics.

## Import a ready-made Kubernetes dashboard

# - - - Instead of creating dashboards from scratch, he imports a template:
- goes to **Dashboards** → **Import**
- He initially tries the wrong ID, then corrects it:
- correct dashboard ID: **3662**
- Selects Prometheus as the data source and imports.
- Outcome:
- “beautiful dashboard” appears with Kubernetes info (nodes, API server, etcd, uptime, etc.).
- He explains what happened:
- Grafana.com dashboard templates contain predefined PromQL queries.
- The dashboard ID maps to a template that runs those queries automatically.

## Expose kube-state-metrics and show its /metrics endpoint

### ### kubectl expose (service) for kube-state-metrics

# - - - He wants to show the kube-state-metrics metrics output directly.
- Exposes the service (TargetPort he identifies: **8080**) and names it `kube-state-metrics-ext`.
- `kubectl get svc` shows the new NodePort (he cites `30421`).
- Opens in browser:
- `http://<minikube-ip>:30421/metrics`
- Shows:
- the endpoint returns a large “metrics format” output (lots of cluster object metrics).

### How Grafana vs Prometheus differs (re-stated here)

# - - - Prometheus query results show raw output.
- Grafana presents the same information visually.

## Scraping additional endpoints in Prometheus (config map edit)

### ### Prometheus scrape_configs (ConfigMap)

# - - - He explains how to pull kube-state-metrics into Prometheus directly:
- `kubectl get cm` shows Prometheus server config map.
- `kubectl edit cm prometheus-server`
- In the Prometheus configuration (prometheus.yaml):
- find `scrape_configs`
- add a new `job_name` (e.g., “state-metrics”)
- add `static_configs` with the target endpoint (minikube IP + NodePort shown earlier)
- He generalizes the same approach for app metrics:
- ask developers to expose a `/metrics` endpoint via Prometheus libraries
- add that endpoint into Prometheus scrape configs

## Wrap-up: what was accomplished

# - - - Set up:
- Prometheus
- Grafana
- imported dashboard template (ID **3662**) showing cluster-level metrics
- exposed and inspected kube-state-metrics `/metrics`
- explained how to add new scrape targets via Prometheus ConfigMap
- He points viewers back to his GitHub repo for step-by-step replication (mentions kube-state-metrics note may be missing but is “just one command”).

#