# DevOps Day 27: Docker Bind Mounts vs Volumes (Why They Exist + Hands-on)

## Intro: why bind mounts/volumes feel “complicated”

# 

- Speaker introduces **Day 27** of the complete DevOps journey.
- Topic: **Docker bind mounts and volumes**.
- Notes this is where many people “get disconnected” and feel Docker becomes complicated.
- Goal: explain the **practical problems** that led to bind mounts/volumes, then show them **theory + practical**.

## The core problem: containers are ephemeral (data disappears)

### Problem 1: logs disappear when a container dies (Nginx example)

# 

- Example: an **Nginx** app inside a container writes important **log files**:
- logged-in user info
- IP address used to access the app
- Logs matter for security/auditing (e.g., last 10 days / last week, depending on org needs).
- If the container goes down, the log file gets deleted.
- Speaker’s definition: *“containers are ephemeral in nature… they are very short-lived.”*
- Reconnects to earlier lessons: containers don’t have a permanent filesystem by default; they use host/kernel resources, and when they die, resources are freed.

### Problem 2: front end and back end need shared persistent files

# 
- Example: two containers:
- **Front-end container**
- **Back-end container**
- Back end continuously writes files that front end must read (examples mentioned):
- JSON, YAML, XML
- or dynamically generated HTML files (“yesterday’s,” “today’s,” “10 days ago”)
- If the back-end container goes down and there’s no persistent storage, prior files are gone.
- Result: front end can only serve “today’s” content; requests for older records fail.

### Problem 3: a container needs to read files from the host (cron job scenario)

# 
- Example: a **cron job** (speaker says “cronzer,” meaning periodic job) runs on the host or elsewhere and creates a file on the host filesystem.
- A container needs to read that host file and show it to users.
- Issue: without mounts, there’s no standard way (in what they’ve learned so far) for the container to access a specific host directory/file.

## Docker’s solutions: bind mounts and volumes

# 
- Docker provides two options:
1. **Bind mounts**
2. **Volumes**

### Bind mounts (concept)

# 
- Bind mount “binds” a host directory to a container directory.
- Example described:
- Host has `/app`
- Container (C1) has `/app`
- Bind mount links them
- Outcome:
- Container can read/write files in that directory.
- If container C1 goes down, data still exists on the host’s `/app`.
- A new container (C2) can be started and bound to the same host directory, so data isn’t lost.

### Volumes (concept + why they’re often better)

# 
- Volumes solve similar persistence/sharing problems but provide a “better life cycle.”
- Speaker’s description of volume:
- *“a logical partition”* created on the host and managed via Docker.
- Volume lifecycle management via Docker CLI:
- create a volume
- destroy a volume
- detach from container C1 and attach to C2
- attach the same volume to C1 and C2
- Advantages emphasized:
- Managed through Docker commands (clear lifecycle).
- Not restricted to binding a specific host path like `/app`.
- Can use external storage systems (examples mentioned): **S3**, **NFS**, external EC2 instance/storage devices.
- Helps when host disk is limited, when large storage is needed, or when backups are required.
- Can be high performance (e.g., high IO read/write storage mounted into containers).
- Speaker’s guidance: prefer **volumes** unless you have a specific use case for bind mounts.

## `-v` vs `--mount` (why both exist)

# 
- Both do the same thing: mount storage into a container.
- Difference is syntax:
- `-v`: shorter; parameters are packed into one expression (source, destination, options separated via colon/comma style).
- `--mount`: more **verbose** and self-describing (explicit keys like source/target/permissions).
- Recommendation: use `--mount` in organizations because it’s easier for others to read and understand.

## Hands-on: volume lifecycle and mounting

### ### Docker volume commands (lifecycle)

# 

1. List volumes:
- `docker volume ls`
- Speaker shows existing volumes (e.g., “argocd,” “demo,” “local volumes”).
2. Create a volume:
- `docker volume create Abhishek`
3. Inspect a volume (find details like driver + mountpoint):
- `docker volume inspect Abhishek`
- Output includes:
- creation timestamp
- driver = local
- mountpoint path under Docker’s volumes directory
- scope = local
4. Remove volumes:
- `docker volume rm <name>`
- Can remove multiple volumes in one command by listing names.

### ### Docker container commands used in the demo

# 
- To run a container with a volume mount, he uses detach mode and `--mount`:
- `docker run -d --mount source=Abhishek,target=/app <image>`
- Uses **nginx:latest** as a simple image example.
- To view running containers:
- `docker ps`
- To confirm mount details on a container:
- `docker inspect <container>` and search for “Mounts”
- Shows:
- volume name (source)
- host mountpoint path
- container destination (e.g., `/app`)
- access mode (read/write)

### Important operational note: deleting volumes in use

# 
- If you try `docker volume rm Abhishek` while a container is using it, Docker errors (“volume is in use”).
- Required order:
1. stop the container
2. delete the container
3. then delete the volume

## Wrap-up: what you achieved

# 
- You created volumes and mounted them into containers.
- Even if the container goes down, data in the mounted directory persists in the volume and can be:
- stored
- backed up
- shared with other containers
- Speaker offers to share additional documentation links and invites questions with timestamps.

# 

# 

#