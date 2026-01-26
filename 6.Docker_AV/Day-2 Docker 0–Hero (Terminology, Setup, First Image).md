# DevOps Day 24: Docker 0–Hero (Terminology, Setup, First Image)

## Intro: prerequisites + materials

# 

- Speaker introduces **Day 24** of the complete DevOps course: **“Docker 0 to Hero.”**
- Strong recommendation: watch **Day 23** first (containers basics, VM vs container, “inception of containers”).
- Mentions a **GitHub repository** created for this lesson:
- Intended as a reference if you don’t follow the video fully.
- Encourages starring/following it.

## What this class will cover (roadmap)

# 
1. Deep dive: **why containers are lightweight** (more detail than Day 23).
2. Explain **Docker life cycle**.
3. Explain **Docker architecture** (components and how you interact with Docker).
4. Learn Docker **terminologies** (naming conventions used by the community):
- Docker daemon
- Docker image
- Dockerfile
- Docker registry
- Docker Desktop
5. Demo: **install Docker** on an AWS **EC2 instance** (Ubuntu).
6. Show common beginner issues:
- starting Docker daemon
- granting user access (so you don’t hit permission errors)
7. Write a **first Dockerfile**, build an image, run a container.
8. Push the Docker image to a **public registry** (and mentions private vs public registries).

- Mentions this is **Part 1**; **Part 2 (tomorrow)** will cover advanced Docker:
- multi-stage Dockerfile
- reducing image size / best practices
- more Dockerfiles/images
- Docker interview questions

## Why containers are lightweight (deep dive)

# 
- Core contrast reiterated:
- VM has a **complete guest operating system**, so it’s heavy.
- Container does not have a full OS; it has a base image/minimal OS and uses host OS resources.

### Kernel dependency explanation

# 
- Speaker defines kernel in context:
- *“Kernel is heart of your operating system.”*
- Docker installs on a host OS and tells containers they can use required resources from the **kernel**, while keeping only minimal system dependencies in the container itself.

### Why containers still need some system dependencies (isolation)

# 
- Containers must be isolated (e.g., “project one” container vs “project two” container).
- If all containers fully share everything, a hacker in one container could access another container.
- Speaker emphasizes the need for *logical isolation* between projects (A, B, C, etc.).

### Container filesystem basics (what’s inside the container)

# 
- Speaker lists common folders present inside containers (as part of container/base image) to form isolation:
- `/bin` (binary execution files)
- `/sbin` (system binaries)
- `/etc` (configuration files for system services)
- `/lib` (library files)
- `/usr` (user-related files)
- `/var` (log files and other variable data)
- `/root` (home directory for root user)
- Point made: containers shouldn’t share these folders with other containers, or security is compromised.

### What containers use from the host OS / kernel

# 
- Containers use kernel-related components such as:
- host filesystem (speaker mentions “file system”)
- networking stack
- system calls
- namespaces
- control groups (cgroups)
- Speaker references an earlier Linux OS/kernels video for deeper context.

### Interview framing

# 
- Speaker says many people repeat “containers don’t have an OS” without understanding it.
- His message: containers have a **base OS/minimal dependencies** to support isolation, and use the host kernel for the rest.

## How small is a container image? (size comparison example)

# 
- Example: **official Ubuntu Docker image**:
- Size cited: **28.16 MB** on **Linux AMD64**.
- Compared to Ubuntu VM image:
- Example size cited: **~2.3 GB**.
- Speaker emphasizes the magnitude: “almost 100 times smaller.”
- Practical implication:
- You can run “10 to 20 or 30 to 40 containers” on a VM, depending on resource use.
- If containers are not running, they don’t consume resources like a VM’s always-present guest OS does.
- Clarifies exceptions:
- If containers are resource-hungry or you reserve resources (e.g., “256 MB”), you’ll hit limits differently.

## What Docker is (simple definition + alternatives)

# 
- Simple definition given:
- *Containerization is a concept, and Docker is a platform that implements containerization.*
- Mentions other platforms exist:
- **Podman** as a competitor (addresses some Docker challenges, like centralized daemon).
- Guidance:
- Start with Docker because it’s easier to learn and troubleshoot.
- Then move to tools like **Buildah**, **Podman**, **Skopeo** later.

## Docker architecture (high-level)

### ### Docker CLI (client)

# 

- As a user, you run Docker commands via the **Docker CLI**.

### ### Docker daemon

# 
- Speaker definition-style explanation:
- *Docker daemon is a process/service that listens to your Docker requests and acts on them.*
- Flow:
- Docker CLI commands → received by Docker daemon → daemon builds images, runs containers, pushes/pulls images to/from registries.
- “Heart of Docker”:
- Docker daemon is described as the heart; if it goes down, Docker stops functioning / containers are affected.

## Docker life cycle (workflow)

# 
1. Write a **Dockerfile**.
- Definition: *Dockerfile is “a set of instructions.”*
- Example instructions include:
- pick a base image (e.g., Ubuntu)
- copy source code into image
- install dependencies (e.g., `npm`, `pip`)
- start the app (run server/commands)
2. Use **docker build** to submit the Dockerfile to Docker daemon → creates a **Docker image**.
- Image compared to a VM snapshot (speaker: image is like a “snapshot”).
3. Use **docker run** to run the image → creates a **Docker container**.
4. Share container/image via a registry so others can run it without installing dependencies manually.

- Speaker notes Docker reduces complexity:
- Instead of users performing many manual setup steps, one Dockerfile creates a reusable runnable image.

## Docker terminology (as presented)

### ### Docker daemon (“docker d”)

# 

- In Linux, a daemon is like a service.
- In Docker, it “listens” for CLI/API requests and executes them.

### ### Docker Desktop

# 
- Speaker says to “ignore for 10–15 minutes” initially; later clarifies during install segment.

### ### Docker Registry

# 
- Definition: *Registry is “a place where you store your Docker images.”*
- Example public registry: **Docker Hub**.
- Notes:
- Registries can be **public or private**.
- Even public registries can host **private repositories** (analogy to GitHub private repos).
- Mentions another registry example: **Quay.io** (“quay.io is another public registry”).
- Example: Ubuntu publishes its **official Ubuntu Docker image** so others can use it as a base image.

### GitHub vs Docker Hub (interview-style comparison)

# 
- GitHub: stores **source code** (version control for code).
- Docker Hub: stores **Docker images** (version control platform for images).

## Example for today: a simple Python app

### ### Python (app.py)

# 

- In the repo’s example folder:
- `app.py` is a simple Python program printing “hi/hello world.”
- “Without Docker” steps described (manual approach):
- create an EC2 instance (or use a laptop)
- install Python
- install pip
- install dependencies
- handle platform issues if any
- “With Docker” advantage described:
- QA/testing team can just download the Docker image and run it.

## Installing Docker on an EC2 instance (Ubuntu)

# 
- Speaker uses a pre-created EC2 instance named “Docker demo” running Ubuntu.

### ### SSH (login)

# 
- Logs into EC2 via SSH using a `.pem` key.

### ### apt update / apt install docker.io

# 
1. `sudo apt update`
2. `sudo apt install docker.io -y`

- Explains `-y` avoids interactive “do you want to proceed” prompts.

### ### systemctl status docker

# 
- Checks Docker service:
- `sudo systemctl status docker`
- Confirms Docker is “active” (running).

## Common beginner issue: permission denied running Docker

### ### docker run hello-world

# 

- Runs `docker run hello-world` and gets a **permission denied** error.
- Explanation:
- Docker was installed with sudo/root; Docker daemon runs as **root** by default.
- Speaker calls this a Docker drawback:
- daemon runs as root (security concern)
- daemon is a single monolithic process (if it goes down, containers are affected)

### ### usermod: add user to docker group

# 
- Fix: add the Ubuntu user to the Docker group using `sudo usermod ...` (adds user to `docker` group).
- Note: change doesn’t take effect immediately.
- Need to **log out and log back in**, or source the user profile (speaker mentions `source` but keeps it simple).
- After re-login, `docker run hello-world` works.

## Writing and building the first Dockerfile

### ### git clone (get the repo)

# 

- Clones the GitHub repository on the EC2 instance and navigates to it.
- In `examples/` folder:
- `app.py`
- “first Dockerfile”

### ### Dockerfile (first example)

# 
- Speaker walks through the file content:
- `FROM ubuntu:latest` (base image pulled from a public registry like Docker Hub)
- `WORKDIR /app` (sets working directory inside the image)
- `COPY . .` (copies current folder contents into `/app`)
- Install Python (so the image contains required runtime)
- `CMD python3 app.py` (command executed when the container starts; described as entry-point-like)

### ### docker build (with tag)

# 
- Runs `docker build` using the current directory (`.`) and a tag.
- Explains why tagging matters:
- without a tag you rely on an image ID, which is hard to remember
- Tag format explained in practice:
- `<dockerhub-username>/<repo-name>:<tag>`
- He uses his Docker Hub username and a repository name, with a tag like `latest` (and mentions tags like v1/v2 etc.).

## Running the container

### ### docker run -it

# 

- Runs the image using `docker run -it ...`
- Output shows the Python app prints **“hello world.”**
- Notes:
- This is a command-line app; a web app would run a server and could be accessed via browser/EC2 instance.

## Pushing the image to Docker Hub (public registry demo)

### ### docker login

# 

- Logs in via `docker login` using Docker Hub username and password.

### ### docker push

# 
- Pushes the tagged image using `docker push <username>/<repo>:latest`.

### Verify in Docker Hub UI (result)

# 
- Logs into Docker Hub in the browser.
- Observes:
- a new **repository** created (e.g., “my-first-docker-image”)
- a tag named **latest**
- Notes how others can consume it:
- copy the pull command shown in Docker Hub
- `docker pull ...`
- verify locally with `docker images`

## Wrap-up

# 
- If anything is unclear, speaker asks viewers to share timestamps.
- Reassures: Docker commands will be explained in detail in **Part 2** (tomorrow).
- Encourages following the GitHub repo for additional interview-helpful notes.
- Closes with like/share/subscribe reminder.