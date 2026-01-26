# DevOps Day 41: Kubernetes ConfigMaps & Secrets (Concepts + Live Demo)

## What you’ll learn today

# 

- Speaker introduces **Day 41**: **ConfigMaps** and **Secrets** in Kubernetes.
- Agenda (in order):
1. What is a ConfigMap?
2. Why Secrets exist (the “why” aspect)
3. Interview question: difference between ConfigMap and Secret
4. Live demo:
- create ConfigMap
- create Secret (mentions different types)
- reference/use them in a Pod/Deployment
- Notes it’ll be a long video; also asks viewers to subscribe for future free courses.

## ConfigMap: the problem it solves (application example)

# 
- He asks you to momentarily forget Kubernetes and think like an application developer.
- Example flow:
- backend application talks to a database, retrieves info, returns it to the user.
- The app needs configuration such as:
- DB port
- DB username
- DB password
- connection type
- number of connectors
- Best practice:
- *application should not have these details hard-coded.*
- if hard-coded values change (username/password/port), the app fails or returns wrong/vague results.
- Typical non-Kubernetes approach:
- store values in **environment variables** or a **file** on the filesystem
- read them through OS/library support (mentions Python OS module, Java OS libraries)

### What Kubernetes suggests

# 
- For non-sensitive configuration like DB port/connection type (he explicitly sets aside username/password for the moment):
- Kubernetes provides **ConfigMap**.
- Definition-style phrasing:
- *ConfigMap is used to “store data” that can later be used by your application/pod/deployment.*
- How the data can be consumed in a pod:
- as **environment variables**
- as **files via volume mounts**

## Why Secrets exist (same need, but for sensitive data)

# 
- Secrets solve the same general “store and pass config” problem but for sensitive data.
- Sensitive examples:
- DB username
- DB password
- Risk explanation:
- Kubernetes resources are stored in **etcd**.
- If sensitive values were in a ConfigMap, an attacker who accesses etcd (or the resource) could read them, compromising the database/platform.

### What changes with Secrets

# 
- Definition-style phrasing:
- *Secrets deal with “sensitive data.”*
- Kubernetes behavior:
- *Secrets are “encrypted at rest” before being saved to etcd.*
- Kubernetes also allows “custom encryption” (pass values to the API server so it encrypts before writing to etcd).
- Result:
- an attacker reading etcd would only get encrypted data without the decryption key.

### Remaining risk: someone can still read Secrets via kubectl

# 
- He points out the other access path:
- if someone has cluster access, they could `kubectl describe` / `kubectl edit` to see secret content.
- Kubernetes recommendation:
- enforce strong **RBAC** and **least privilege**:
- developers may read pods/deployments/configmaps
- but should not have access to secrets unless required

### Interview answer summary he gives

# 
- Both ConfigMaps and Secrets are used to store key/value style data for apps running in pods.
- **ConfigMap**: non-sensitive info.
- **Secret**: sensitive info + encrypted at rest + restrict access via RBAC.

## Live demo begins (terminal)

### ### kubectl (cluster cleanup)

# 

- He cleans up prior resources:
- `kubectl get deploy`
- deletes deployment `sample-python-app`
- `kubectl get cm`
- deletes ConfigMap `test-configmap` (then restarts demo from scratch)

## Demo 1: Create a ConfigMap (YAML method)

### ### ConfigMap YAML (cm.yaml)

# 

- Creates `cm.yaml` with:
- `apiVersion: v1`
- `kind: ConfigMap`
- `metadata.name: test-cm`
- `data:`
- `DB-Port: 3306` (MySQL port)
- Applies it:
- `kubectl apply -f cm.yaml`
- Verifies:
- `kubectl get cm`
- `kubectl describe cm test-cm` (shows stored key/value)

## Demo 2: Use ConfigMap as an environment variable in a Deployment

### ### Deployment YAML (deployment.yaml)

# 

- Applies an existing deployment YAML (Python Django app from his earlier “Docker Zero to Hero” content):
- `kubectl apply -f deployment.yaml`
- Checks pods:
- `kubectl get pods -w`
- Exec into a pod:
- `kubectl exec -it <pod> -- /bin/bash`
- Confirms no DB env var exists initially:
- checks environment variables and sees nothing for DB.

### Add env var sourced from ConfigMap

# 
- Edits `deployment.yaml` to add `env:` in the container:
- env var name: `DB_PORT` (caps)
- value pulled from ConfigMap using `valueFrom`
- First apply attempt fails due to wrong field name:
- error: unknown field `configMapRef`
- He corrects it to:
- `configMapKeyRef` (as he says: “always follow the documentation”)

### Re-apply and verify

# 
- Applies updated deployment:
- pods roll (old terminate, new create)
- Exec into a new pod and verify:
- `DB_PORT` is present and equals `3306`

## Problem with env var approach: updates don’t propagate

# 
- He changes the ConfigMap port value (e.g., 3306 → 3307).
- Observation:
- pod environment variable remains `3306`.
- Core reason (his wording):
- *“changing the environment variables inside containers is not possible”*; you’d need to recreate the container.
- He notes production concern:
- restarting deployments/containers can cause traffic loss.

## Demo 3: Use ConfigMap via volume mount (file-based config)

### ### Volume mounts (ConfigMap as a file)

# 

- He removes the `env:` approach and switches to volume mounts.

#### Step A: define a volume sourced from the ConfigMap

# 
- Adds a volume:
- volume name: `db-connection`
- source: `configMap`
- configMap name: `test-cm`

#### Step B: mount the volume into the container filesystem

# 
- Adds `volumeMounts`:
- name: `db-connection`
- mountPath: `/opt` (example path)

### Apply and verify inside the pod

# 
- Applies deployment; pods restart.
- Exec into a pod:
- confirms `/opt` contains a file named like the key (DB port key)
- `cat /opt/DB-Port` shows value `3306`

### Proves updates propagate without restarting the pod

# 
- Updates ConfigMap and applies:
- `kubectl apply -f cm.yaml`
- Confirms pods did not restart (timestamps unchanged).
- Then reads the mounted file again:
- value updates (e.g., to `3307`, later `3309`) after a short delay.
- He notes:
- it can take a few seconds for the mounted file to reflect the change because Kubernetes keeps reading for changes.

## Demo 4: Create a Secret (CLI method) + what you see

### ### Kubernetes Secrets (generic vs TLS mention)

# 

- Mentions there are different secret types:
- **TLS secrets** (for certificates)
- **generic secret** (used here)

### ### kubectl create secret generic

# 
- Creates a generic secret via literals:
- `kubectl create secret generic test-secret --from-literal=DB-Port=3306`
- Describes it:
- `kubectl describe secret test-secret`
- output shows `DB-Port: 4 bytes` (not the plaintext)
- Edits it:
- `kubectl edit secret test-secret`
- he says it appears “base64 encrypted” (base64-encoded)
- Shows decoding:
- use `base64 --decode` to confirm it decodes back to `3306`

### Security note he makes (within the transcript)

# 
- He says default secret encoding/encryption “is not great” and mentions tools/approaches:
- **HashiCorp Vault**
- **sealed secrets**
- plus encrypting secrets at etcd via API server encryption key configuration (he references earlier security discussion)

## Homework he assigns (Secrets usage in pods)

# 
- Exercise:
- create a new secret for DB password (example password like “a b”)
- repeat the same usage patterns as ConfigMap:
- as env var: use `secretKeyRef` instead of `configMapKeyRef`
- as volume: replace `configMap` volume source with `secret`
- He says the Kubernetes docs show the exact syntax and he’ll put the link in the description.

## Wrap-up

# 
- He says he covered ConfigMaps fully; now viewers should practice the same steps with Secrets.
- Ends with: questions in comments, like/share, and subscribe.

# - -

