# DevOps Day 40: Kubernetes Custom Resources (CRD, CR, Controllers) + Istio Example

## Opening + agenda

# - - - Speaker introduces **Day 40**: *custom resources in Kubernetes*.
- Quick announcement:
- asks viewers to subscribe; he’ll share a future roadmap soon (master classes / more free courses).
- Today’s topics (three parts, in order):
1. **Custom Resource Definitions (CRD)**
- shorthand: **CRD**
2. **Custom Resources (CR)**
3. **Custom Controller** (custom Kubernetes controller)

## High-level overview: native resources vs extending Kubernetes

# - - - In a Kubernetes cluster, many resources exist “out of the box,” e.g.:
- Deployment, Service, Pod, ConfigMap, Secret
- Kubernetes idea:
- *If you want to “extend the API of Kubernetes” or “introduce a new resource to Kubernetes,”* you use the custom resource mechanism.
- Why you’d introduce a new resource:
- when the functionality you want isn’t supported by native resources.
- Examples of “new capabilities” mentioned:
- security-related tools/resources: **kube-hunter**, **Kyverno**, **kube-bench** (he lists several security tools while explaining)
- GitOps capabilities: **Argo CD**, **Flux**, **Spinnaker**
- CNCF ecosystem: he frames **CNCF** as “all about the Kubernetes controllers (custom Kubernetes controllers)”
- Two “actors” he calls out:
- **DevOps engineer**: deploys **CRD** and **custom controller**
- **User** (could be developer or DevOps too): creates the **custom resource**

## Scenario setup: you’re fine with native resources, then you discover “more”

# - - - Baseline: organization uses Kubernetes with:
- Deployment + Service + Ingress + ConfigMaps/Secrets
- Ingress controller exists; traffic flows; app is working.
- Then you “explore Kubernetes more” and discover tooling beyond native resources:
- **Istio** adds service mesh capabilities
- **Argo CD** adds GitOps capabilities
- **Keycloak** provides “tight identity and access management / OAuth / OIDC capabilities”
- security tools again referenced (Kyverno, kube-bench, etc.)
- Core scaling problem:
- Kubernetes can’t embed logic for thousands of external products into the control plane.
- Kubernetes solution statement:
- *Kubernetes will “allow you to extend the API of Kubernetes”* by adding new API resources.

## The three pieces: CRD, CR, and custom controller

### ### CRD (Custom Resource Definition)

# - - - Definition-style phrasing from speaker:
- *A CRD is “defining a new type of API to Kubernetes.”*
- *“CRD is a YAML file” used to introduce the new API type.*
- What CRD contains (as explained):
- the “complete” set of fields/options a user is allowed to submit for that custom resource.
- Validation analogy:
- If you add an unknown field (e.g., `XYZ`) into a Deployment spec and apply it, Kubernetes errors like:
- *“field XYZ is not known”*
- That happens because Kubernetes knows the “definition” of a Deployment.
- Same idea for custom resources:
- CRD provides the schema/definition so Kubernetes can validate custom resources.

### ### CR (Custom Resource)

# - - - Definition-style phrasing:
- *A custom resource is “whatever the user is submitting”* based on the CRD.
- Example he uses throughout:
- **Istio** custom resource: **VirtualService**
- User writes YAML with:
- `apiVersion` (Istio-related)
- `kind: VirtualService`
- `metadata`, `spec` (properties come from Istio docs)

### ### Custom Controller (Custom Kubernetes Controller)

# - - - Key point:
- CRD + CR is not enough; something must “watch” the CR and take action.
- Analogy to native Kubernetes:
- Deployment works because a **deployment controller** exists:
- deployment controller → creates ReplicaSet → creates Pods
- For custom resources:
- a **custom controller** must exist to:
- watch the custom resource
- perform the needed action (e.g., Istio service mesh behavior)

## End-to-end flow (repeated as a practical mental model)

# - - - CR is created and validated against CRD, but:
- *if “nobody is watching it,” nothing will happen.*
- He compares it to Ingress:
- *Ingress resource without an Ingress controller → nothing happens.*
- The three recurring steps (his summary):
1. Deploy **CRD** (extend Kubernetes API)
2. Deploy **custom controller**
3. Users in selected namespaces create **CRs** to use the feature

## Example with “actors” and namespaces (Istio VirtualService)

# - - - DevOps engineer actions:
- deploy the **VirtualService CRD** into the cluster (via manifests / Helm charts / operator)
- deploy the **custom controller** (cluster-wide or namespace-scoped depending on controller)
- User actions:
- in a namespace (he uses “Abhishek” namespace as example), create a **VirtualService** custom resource (VS)
- System behavior:
- API server validates the CR against the CRD
- custom controller watches the CR and applies behavior (he mentions Istio features like service mesh / mutual TLS / east-west traffic, without diving deeper)

## Writing custom controllers (overview, not a full tutorial)

### ### Golang (common controller language)

# - - - He says controllers can be written in Go, Python, or Java, but:
- Go is most popular because:
- Kubernetes is written in Go
- Go has strong ecosystem support
- Mentions client libraries:
- **client-go** (primary Go client for Kubernetes API)
- notes other clients exist (Python/Java)

### ### client-go + watchers (high-level mechanics)

# - - - He describes the approach:
- set up **listeners/watchers**
- Kubernetes has built-in watchers for native resources (deployments/services).
- For custom controllers, you create new watchers for your custom resource.
- Notes evolution:
- early days (around 2015): fewer frameworks; you wrote more from scratch.
- now frameworks exist.

### ### controller-runtime (framework)

# - - - Mentions **Kubernetes controller-runtime** (Go package) to set up watchers more easily.

### ### Work queue / processing loop (as described)

# - - - Watchers detect create/update/delete events.
- Mentions a client-go concept/package called **reflector** (he explicitly avoids deep details).
- Events get placed into a **worker queue**.
- Controller processes objects in the queue and creates the required functionality.

### ### operator-sdk (mentioned, not taught)

# - - - Mentions: if you want to write operators, you can use **Operator SDK**.
- Says operator vs controller is not today’s topic.

## Where to learn more (his pointers)

# - - - He says most DevOps roles don’t require writing controllers; concept + deployment/debugging is often enough.
- If interested in writing controllers, he points to:
- “Kubernetes sample custom controller” repo/documentation (he says he’ll put link in description)
- Kubernetes documentation supporting sample controller patterns.

## Demo-style references: CNCF projects and Istio installation

### ### CNCF (project landscape)

# - - - He recommends looking at **CNCF** to see many popular controllers/projects.
- Mentions examples in the landscape:
- Argo CD, Istio, Backstage, Buildpacks, containerd, CoreDNS, Crossplane, Prometheus (as examples of popular projects)

### ### Istio (example of CRDs + controller installation)

# - - - He shows the **Istio GitHub repo** and **istio.io** documentation.
- Installation approach he highlights:
- use **Helm charts** (he says Helm is popular)
- Helm install deploys both:
- the **CRDs**
- the **custom controller(s)**
- Notes a typical doc-driven flow:
- add Istio Helm repo
- update repo
- create an `istio-system` namespace (he demonstrates this step)
- Verification he shows:
- `kubectl get crd` to see Istio-related CRDs created (many appear)
- Emphasizes DevOps expectations:
- not just installing; you must understand the product (e.g., Istio):
- read documentation
- debug issues (controller logs, CR status/describe)
- understand concepts like destination rules / Envoy proxy (he names these as examples)

## Wrap-up

# - - - Reiterates goal was to explain the **concept**: CRD + CR + controller, and how deployment typically works (often via Helm).
- Invites questions in comments; closes with subscribe and see you next video.

