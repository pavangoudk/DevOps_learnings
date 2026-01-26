# DevOps Day 28: Docker Networking (Bridge, Host, Custom Networks)

## Intro: why Docker networking now

# 

- Speaker introduces **Day 28** of the complete DevOps course.
- Topic: **Docker networking** (also called container networking).
- Focus stays on Docker because the course has used Docker to explain container concepts in the last several classes.
- Recommends watching prior videos because the course follows a “hierarchy” that will help later with **Kubernetes** as well.

## Why you need networking in Docker

# 
- Networking enables:
- containers to **communicate with each other**
- containers to communicate with the **host system**
- Example host setup:
- A “host” (could be EC2, physical server, etc.) with Docker installed.
- Multiple containers on that host.

### Two scenarios the speaker sets up

# 
1. **Container 1 talks to Container 2**
- Example: **front-end container** must talk to **back-end container**.
2. **Container 1 must be isolated from Container 2**
- Example: **login container** should not be able to reach a **finance/payments container** holding sensitive data (credit/debit card info, user details, etc.).

## VM vs container networking (context)

# 
- With virtual machines:
- Each VM has its own OS and can be placed in different subnets.
- Speaker gives example subnet separation like `172.16.x.x` vs `172.18.x.x` to show isolation flexibility.
- With containers:
- You may need both communication *and* isolation, depending on the application.

## How a container talks to the host (default behavior)

# 
- Speaker’s example:
- Host network interface: **eth0** (out-of-the-box).
- Host IP example: `192.16.3.4`
- Container IP example: `172.17.0.2`
- Because subnets differ, direct ping/communication would fail without help.

### Definition + default Docker networking

# 
- Docker creates a virtual ethernet bridge named **docker0** to enable connectivity.
- Speaker states:
- *“This is called as Bridge networking.”*
- *“What is the default networking in Docker? The default network is a bridge networking.”*
- Key implication:
- Without this bridge (docker0), containers wouldn’t be reachable; hosted applications wouldn’t be accessible to users.

## Other Docker network types (overview)

# 
- Docker networking options discussed:
1. **Bridge** (default)
2. **Host**
3. **Overlay** (mentioned as more relevant for Kubernetes / Docker Swarm)

### ### Host networking

# 
- Container uses the host’s network directly (shares host network namespace).
- Speaker frames it as:
- container and host effectively share the same subnet.
- makes access easy, but **less secure**.
- Security concern noted:
- If containers share host networking, “whoever has access to your host can directly have access to your container.”

### ### Overlay networking

# 
- Described as useful when you have **multiple hosts** in a cluster and need a common network across them.
- Speaker defers deep details to Kubernetes / Docker Swarm.
- Says it’s “too much” if you’re only running containers on a single Docker host.

## Why default bridge networking can be insecure (container-to-container reachability)

# 
- With default bridge networking:
- all containers share the same bridge path (docker0).
- result: containers can ping/talk to each other by default.
- This may be fine for front-end ↔ back-end, but not for login ↔ finance.
- Speaker’s concern: a “common path” exists that could be abused.

## Solution: custom bridge networks for isolation

# 
- Key point:
- Docker allows creating **custom bridge networks** (multiple bridge networks).
- Approach described:
- Keep less-sensitive containers on default bridge (docker0).
- Put sensitive containers (e.g., finance) on a **separate custom bridge network**.
- Effect:
- breaks the “common path,” creating logical network isolation between container groups.
- When running a container, you can pass the target network via `--network`.

## Practical demo (terminal walkthrough)

### ### Docker (nginx containers for demo)

# 

- Speaker uses **nginx** containers as examples.

### Step 1: Start two containers on default bridge

# 
- Runs an nginx container named **login** in detached mode.
- Enters the container with:

- `docker exec -it login /bin/bash`
- Installs ping tooling inside the container:

- `apt update`
- installs package providing ping (mentions **iputils-ping**)
- Runs a second nginx container named **logout**.

### Step 2: Verify container IPs and connectivity

# 
- Lists containers:
- `docker ps`
- Finds IP addresses via:
- `docker inspect <container>`
- Observed IPs:
- login: `172.17.0.2`
- logout: `172.17.0.3`
- From login container, pings logout container IP successfully.
- Conclusion: containers on default **bridge** network can communicate.

### ### Docker network ls / rm

# 
- Lists networks:
- `docker network ls`
- Notes default networks:
- bridge
- host
- none
- Deletes a previously-created test network example:
- `docker network rm test`

### Step 3: Create a custom bridge network

# 
- Creates network:
- `docker network create secure-network`
- `docker network ls` shows the new custom network.

### Step 4: Run a sensitive container on the custom network

# 
- Runs nginx container named **finance** with:

- `--network=secure-network`
- `docker inspect finance` shows:

- network: `secure-network`
- IP example: `172.19.x.x` (different subnet than default bridge containers)
- Speaker instructs to try pinging finance from login to confirm isolation (expects it won’t reach).

### Step 5: Host networking demo

# 
- Runs container named **host-demo** with:
- `--network=host`
- `docker inspect host-demo` shows:
- network: `host`
- no separate container IP shown (because it’s bound to host networking; Docker doesn’t create a virtual network/IP for it)

## Wrap-up + what’s next

# 
- Speaker summarizes: networking lets containers communicate and also lets you isolate sensitive containers via custom bridge networks.
- Mentions original plan was to move from Docker networking directly into Kubernetes (to show how Kubernetes solves scaling/auto-healing/networking challenges), but he’ll add a session for **Docker interview questions** since viewers requested it.
- Closes with like/comment/share reminders.

# 

# 

# 

#