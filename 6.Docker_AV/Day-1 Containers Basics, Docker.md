# DevOps Day 23: Containers Basics, Docker Intro, and Buildah Overview

## Intro + announcement

- Speaker introduces **Day 23** of the “complete DevOps course.”
- Topic: an introduction to **containers**.
- Announcement: created a **Telegram group** to share useful resources (e.g., Medium blogs, other references).
- Reason: easier to find and revisit resources than YouTube community posts.
- Invites viewers to join via the link in the description.

## What the class will (and won’t) do today

- Focus: fundamentals / “basics or introduction of containers.”
- Speaker sets expectation: **no projects** and **no Docker commands deep-dive** today—this is an intro to build foundational understanding.
- Also plans to introduce:
- **Docker** (what it is, at a high level)
- **Buildah** (because later projects will use it)
- Mentions Buildah is important because it integrates well with modern tools like **Skopeo** and **Podman**.

## Prerequisite: understand virtual machines first

- Speaker recommends watching **Day 3** (virtual machines) before this class.
- Concept progression (speaker’s framing):
- Virtual machines are an “advancement to physical servers.”
- Containers are an “advancement to virtual machines.”

## Virtual machines refresher (physical server → virtualization)

- Starts with a physical server (could be IBM/HP hardware or even your laptop).
- Problem: physical server resources (CPU/RAM/hardware) are often **underutilized**, especially at organization scale (“thousands of servers”).
- Solution introduced: **hypervisor** and **virtualization**.

### Definition: virtualization

- *“Virtualization is basically used to create a lot of virtual machines or virtual servers on top of your physical servers.”*
- VM described as:
- a **logical separation / logical isolation**
- each VM has its **own operating system**
- apps run on top of that OS
- Security/isolation note:
- VMs are “tightly separated” because each has its own OS, even if they share the same physical host.

## Why containers if virtualization already helps?

- Speaker sets up the core motivation: even after adopting VMs, organizations still waste resources.

### VM resource waste example (numbers)

- Physical server example: **100 GB RAM + 100 CPU**.
- Split into **4 VMs** (e.g., 25 GB RAM each) with OS installed per VM.
- AWS analogy:
- AWS buys/builds physical servers, installs hypervisors (mentions **Xen hypervisors**), and users create **EC2 instances** (VMs) by choosing OS, etc.
- Waste scenario:
- App on VM1 might peak at only **10 GB RAM and ~6 CPU**, leaving large unused capacity.
- When load is low or zero, even more resources are wasted.
- EC2 observation:
- Speaker asks how often people see EC2 “running out of memory”—usually “very, very less,” implying typical over-provisioning.
- Organization-scale implication:
- If an org runs “1 million EC2 instances” and most waste resources, it becomes a major cost issue.
- Conclusion: containers emerged to **use VM resources more effectively** (solve some VM inefficiencies).

## Security comparison: VMs vs containers (high-level)

- Speaker frames it as “to some extent” improvements:
- VMs solved some physical-server issues.
- Containers solve some VM issues, but containers have drawbacks too.
- One-line contrast (speaker’s gist):
- VMs are more secure because they have a **full operating system** and “complete isolation.”
- Containers do not have a full OS; isolation exists but “is not complete,” and containers can share resources with the host OS.

## Container architecture: where containers run

- Containers can be created in two models:

1. **Model 1 (on physical servers)**

- Physical server → install **Operating System** → install **Docker (containerization platform)** → run multiple containers.
- Speaker notes: to create containers, you need a **containerization platform** (like you need a hypervisor for VMs).
2. **Model 2 (on virtual machines)**

- Physical server → create **VM/EC2 instance** → install **Docker** → run containers.

- Speaker says Model 2 is more common now because:
- Maintaining physical servers/data centers adds maintenance overhead (patches, security fixes).
- Cloud reduces that overhead, so organizations shift toward Model 2.
- Cloud framing: on AWS/Azure you usually “start from” creating VM/EC2 (you don’t see the physical server layer).

## Why containers are lightweight

- Speaker highlights the main difference: containers are “very lightweight in nature.”

### Definition: what a container is (as described)

- *“A container is… a package or it’s a bundle which is a combination of your application plus your application libraries (dependencies) plus system dependencies.”*
- Key nuance:
- Containers don’t have a **complete** OS, but they may include a **minimal OS/base image** and required system packages.
- They also **share** certain host OS resources/libraries (speaker mentions shared libraries like “etc” and other host resources).

### Size/portability example

- VM backup/image:
- create a **snapshot**, often ~**1–3 GB** (example sizes given).
- Container image:
- often in **MBs**, e.g., ~**100 MB**, maybe **500 MB** depending on dependencies.
- Mentions factors like base image choice and “multi-stage Docker” (not explained here).
- Result: because containers/images are smaller, they are easier to “ship” (transfer/deploy) to platforms like Kubernetes.

### Clarification the speaker makes

- Don’t say containers have **no OS**.
- Say: *they don’t have a “complete or full operating system”; they have minimal OS/system dependencies and share from the host.*

## Docker: why it became popular

- Docker is presented as a containerization platform that made container usage simpler via commands and good UX.
- Core workflow introduced:
- Write a **Dockerfile**
- Docker engine converts it into a **Docker image** (container image)
- Run the image to create a **container**

### ### Docker Engine

- Speaker describes Docker’s dependency on **Docker Engine**, which receives commands and performs:
- Dockerfile → Image
- Image → Container

### Docker lifecycle (as described)

1. Write a **Dockerfile**
2. Build an **image**
3. Run an **image** to create a **container**

### ### docker build / docker run

- Commands mentioned for reference:
- `docker build` (Dockerfile → image)
- `docker run` (image → container)
- Speaker also mentions “run or exec,” but says to consider `docker run` for now.

## Why newer tools: problems Docker faces (Buildah intro)

- Speaker warns this next section may feel complex; says it will make more sense during projects.

### Docker drawbacks mentioned

- Docker relies heavily on Docker Engine, described as a **single point of failure**.
- Definition given: *SPOF = “single point of failure.”*
- If Docker Engine goes down, containers stop working (speaker calls this “terrible” operationally).
- Image build layering:
- Docker builds create many **layers**, which can consume storage.

## Buildah: what it’s for (high-level)

### ### Buildah

- Buildah is introduced as a tool intended to address:
- layer-related concerns
- SPOF concerns
- simplicity
- Integrates well with:
- **Skopeo**
- **Podman**
- Workflow note:
- Buildah is “command”-driven; you can put Buildah commands in a **shell script** to build an image.
- Output compatibility:
- Buildah can create **Docker images** and other **OCI-compliant** images.

## Closing

- Speaker asks viewers to share timestamps where concepts were unclear so he can respond or create a short explainer.
- Encourages like/comment/share and says he’ll see viewers in the next video.