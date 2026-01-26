# DevOps Day 39: Kubernetes RBAC (Concepts) + 30‑Day Free OpenShift Sandbox

## Intro: what’s covered today

# 

- Speaker introduces **Day 39**: Kubernetes **RBAC** (role-based access control).
- Also promises (from thumbnail): how to create a **30-day free OpenShift cluster**, but says he’ll cover that **at the end**.
- Sets expectations for RBAC:
- *“Kubernetes RBAC is a very simple but complicated topic.”*
- It’s simple to understand, but becomes complicated if implemented wrong because it’s directly related to **security**.
- Emphasis: understand the **concept** of RBAC more than memorizing how to create:
- service account
- role
- role binding

(he says those can be done quickly; concept matters most)

## RBAC split into two broad parts

# 
- He divides Kubernetes RBAC into two areas:
1. **Users**
2. **Service accounts / services (applications) running in Kubernetes**

## Part 1: User management (controlling human access)

# 
- Local clusters (minikube/kind) often give you admin access “out of the box” for learning.
- In an organization, the first responsibility for DevOps/Kubernetes admins is to **define access**:
- What can the **development team** do?
- What can the **QA team** do?
- Example risk:
- a QA engineer shouldn’t be able to delete resources in `kube-system` (e.g., anything related to **etcd**), because that can severely disrupt the cluster.
- Definition-style explanation:
- *RBAC means “role based access control”—depending upon the role of the person, you define access.*

## Part 2: Service account management (controlling app access)

# 
- For workloads (pods/apps) running in the cluster, you also need to define permissions:
- Should a pod access **ConfigMaps**?
- Should it access **Secrets**?
- Example risk:
- a malicious pod deleting important components (e.g., API server-related resources) or removing critical things.
- Same RBAC idea applies:
- manage access for services/applications using RBAC.

## The 3 building blocks used to manage RBAC

# 
- He lists three key elements:
1. **Users or service accounts**
2. **Rules / Roles (and ClusterRoles)**
3. **RoleBindings (and ClusterRoleBindings)**

## How Kubernetes handles “users” (important concept)

# 
- Key point: Kubernetes **does not** manage users like a Linux system.
- You can’t run `useradd` inside minikube to create Kubernetes users.
- Definition-style statement:
- *“Kubernetes does not deal with user management”* and instead *“offloads the user management to identity providers.”*

### ### Identity Providers (IdP) / SSO integration

# 
- He uses the “login with Google/Instagram/Facebook” analogy:
- you can access an app without creating a new user inside that app.
- In Kubernetes:
- the **API server** can be configured (via flags) to work like an **OAuth server** for authentication flows.
- Example IdPs he mentions:
- **AWS IAM** users/groups for **EKS**
- **LDAP**
- **Okta**
- other **SSO** providers
- Mentions identity broker option:
- **Keycloak** as a popular broker to connect multiple systems.
- Example scenario: integrate EKS with Keycloak, and Keycloak with GitHub org/users to “get all of these users.”

## Service accounts: what they are and why they matter

# 
- Service accounts are Kubernetes-native and created via YAML.
- Definition-style explanation:
- *“Service account is just like creating a pod”* (a YAML resource with apiVersion/kind/name).
- Default behavior:
- *By default whenever you run a pod, it comes with a default service account.*
- If you don’t create one, Kubernetes attaches a **default service account** to the pod.
- If you create one, you can attach your **custom service account**.
- Pods/apps use their service account to talk to the **API server** when interacting with Kubernetes resources.

## Roles and RoleBindings: granting permissions

### ### Role

# 

- A role is described as:
- *“a yaml file where you will write”* what resources/actions are allowed (e.g., pods, configmaps, secrets).
- Scope distinction:
- Role (namespace-scoped) vs ClusterRole (cluster-scoped), but he postpones deep detail until practicals.

### ### RoleBinding

# 
- Purpose:
- attach the role to a **user** or **service account**.
- He compares roles to **IAM policies** conceptually:
- role defines permissions; binding attaches them to an identity.

### The “simple ecosystem” diagram he describes

# 
- Elements:
- service account (or user)
- role
- role binding
- Behavior:
- *Role* = permissions
- *Service account/user* = identity
- *RoleBinding* = links identity to permissions
- He stresses:
- role without binding → permissions not applied to anyone
- binding without role → nothing meaningful to bind

## Transition: 30‑day free OpenShift cluster (OpenShift Sandbox)

# 
- He moves to the promised OpenShift demo.

### ### OpenShift Sandbox (30-day free shared cluster)

# 
- Opens an incognito browser and searches: **“openshift sandbox”**.
- Notes page offers:
- *“get 30 days free access to shared OpenShift and Kubernetes cluster”*
- Steps:
1. Click **Start your sandbox for free**
2. Create a **Red Hat account** (or use an existing one)
3. Log in
4. Start using the sandbox → cluster is assigned quickly
- He notes it’s a **shared cluster** intended for practice.

### OpenShift console access model

# 
- The cluster UI has:
- developer + administrative tabs
- admin tab has limited access in the sandbox
- Identity provider concept in OpenShift:
- click **login with Dev Sandbox**
- he explains this is like an IdP; in organizations it could be LDAP/Okta.
- Red Hat account acts as the identity source for the sandbox.

### Namespaces in the shared sandbox

# 
- Because it’s shared:
- he is only given access to a **specific namespace created for him**.
- he can switch namespaces in UI but has permissions only in his assigned one.

## CLI login to OpenShift cluster

### ### Copy login command / token

# 

- In UI:
- click username icon → **Copy login command**
- log in again if prompted
- click **Display token**
- Uses token to log in via terminal (CLI).

### ### kubectl (usage shown after login)

# 
- After logging in, he runs:
- `kubectl get pods` (shows pods only in his accessible namespace)

## Create and manage a deployment in OpenShift Sandbox (example)

### ### kubectl create deployment

# 

- He creates an nginx deployment:
- `kubectl create deployment nginx --image ...` (image implied as nginx)
- Confirms in UI under Deployments that **nginx** is created.
- Uses UI to scale:
- scales pods up to **2**.

## What you can practice in the sandbox (as he lists)

# 
- Explore real cluster “feel”:
- deployments/pods
- routes / Ingress (he notes people ask about Ingress)
- services
- storage (persistent volumes/volumes)
- events (image pulled, pods started, etc.)
- API Explorer

## What’s next

# 
- He says **tomorrow’s class** will use the same OpenShift account to do RBAC practicals:
- create service accounts
- create roles
- create role bindings
- Encourages viewers to create the sandbox account before the next video to gain more real-time experience and confidence for interviews.

# - -

