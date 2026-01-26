# DevOps Day 26: Multi-Stage Docker Builds + Distroless Images (Production Focus)

## Intro: why this topic (production + interviews)

# 

- Speaker introduces **Day 26** of the complete DevOps course.
- Format: both **theory + practical**.
- Motivation: help answer interview questions like:
- “What practical problems have you faced with Docker/containers, and how did you solve them?”
- Two main topics (high-level):
1. **Multi-stage Docker builds**
2. **Distroless images**
- Speaker notes a close relationship: using distroless images helps “get maximum benefit” from multi-stage builds.
- Mentions all content is also captured in the **GitHub repository** for hands-on practice.

## Multi-stage builds: first understand the “usual” Dockerfile approach

# 
- Example task: containerize a **calculator application** (assume it’s a Python app).
- Typical high-level Dockerfile steps described:
1. Choose a **base image** (e.g., Ubuntu).
2. Optionally set **WORKDIR**.
3. Install dependencies:
- install Python
- install pip
- install required Python packages/modules
4. Build the artifact/binary (e.g., `.pyc` / egg file idea mentioned).
5. Execute it using **CMD**.

### The problem with the usual approach

# 
- Even though it “looks like a very good Dockerfile,” it has a key issue:
- You only need the **runtime** to execute the app (Python runtime for Python apps, Java runtime for Java apps).
- But the final image includes:
- full Ubuntu base image
- apt tooling/repos
- many build-time tools/packages
- Speaker’s point: **build stage** requirements are different from **runtime** requirements.
- Java example:
- build may need many libraries (pom.xml, jars)
- run only needs *JRE + the built artifact (jar/war/ear)*

## Why Docker introduced multi-stage builds (concept)

# 
- Docker’s solution: **split the Dockerfile into stages**.

### How multi-stage is structured (two-stage baseline)

# 
- Stage 1 (build stage):
- `FROM ubuntu ...`
- install build dependencies
- build the application artifact/binary
- **no CMD/ENTRYPOINT here**
- Stage 2 (final runtime stage):
- `FROM` a **minimal image** (runtime-only) *or* a **distroless** image
- `COPY --from=<stage>` the artifact from stage 1 into stage 2
- define **CMD/ENTRYPOINT only in the final stage**
- Mechanism highlighted:
- use a `COPY --from` syntax
- stage 1 can have an alias like `FROM ubuntu AS build`

### Why this reduces size

# 
- The final image doesn’t carry the build-stage “overload.”
- Final contains only:
- runtime (e.g., Python runtime / Java runtime)
- built artifact/binary
- the executable command

## Multi-stage builds: clearer example with a complex app

# 
- Example: a “three-tier architecture application”:
- Front end: **React**
- Back end: **Java Spring Boot**
- DB: **MySQL**
- Without multi-stage:
- `FROM ubuntu`
- install Java + React tooling + MySQL
- build everything
- run combined artifact (example: `app.ear`)
- image size “can go crazy,” up to ~**1 GB–1.5 GB** (speaker’s illustrative numbers)
- With multi-stage:
- Use a rich build image in earlier stages (even if it’s 1–2 GB; speaker says don’t worry because it won’t be the final image).
- Final stage uses something like:
- `FROM openjdk:<version>`
- `COPY --from=build <artifact>`
- run the artifact
- Example size reasoning:
- OpenJDK image ~100 MB + artifact ~50 MB ⇒ ~**150 MB** final (illustrative)

### Number of stages

# 
- Speaker states: stages can be “countless” (n stages), but there is **one final stage** that should be minimal to get the benefit.

## Distroless images: what they are and why they matter

# 
- Speaker says this concept is “very simple.”

### Definition (as given)

# 
- *“Distroless image is basically a very minimalistic image.”*
- Intended to contain “hardly any packages,” typically **only runtime environment**.

### Practical behavior of distroless (examples)

# 
- In a Python distroless image, even basic commands may not exist:
- `find` might not be found
- `curl` / `wget` might not be available
- sometimes even `ls` may not be available (speaker’s emphasis is on minimalism)
- The purpose: only execute the app (e.g., run the Python app), not provide a full OS toolset.

### Key advantage beyond size: security

# 
- Speaker’s interview framing:
- Moving from Ubuntu/base images (even in final stage) to distroless reduces exposure to OS-level vulnerabilities.
- Example interview answer pattern he suggests:
- Previously used Ubuntu (or richer runtime images) and faced vulnerability concerns.
- Moved to Python distroless image with only Python runtime, lacking common tools, giving “highest level of security.”
- Go-language emphasis:
- Go is “statically typed” (speaker’s phrase) and can be run without a runtime in the final image.
- That enables extremely small images (he cites ~10–15 MB in general discussion, then shows an even smaller case in demo).

## Practical demo setup: GitHub repo + Go calculator

# 
- Speaker returns to his GitHub repo:
- **Docker 0 to Hero** repository
- In `examples/`, a folder: **golang multi-stage docker build**
- Reason for Go:
- best to demonstrate multi-stage + distroless/scratch because Go apps don’t require a runtime in final stage.

### ### Go (calculator.go)

# 
- Demonstrates running locally:
- `go run calculator.go`
- App behavior:
- prompts for calculation input
- examples given:
- “2 * 100” → 200 (speaker’s example)
- “5 / 2” → outputs result

## Demo Part 1: Dockerfile *without* multi-stage

### ### Dockerfile (single-stage, Ubuntu-based)

# 

- Uses `FROM ubuntu` (with an alias “build” though it’s not multi-stage here).
- Installs Go language.
- Sets `GO111MODULE=off` (speaker says don’t worry about it).
- Copies `calculator.go`.
- Builds binary with `go build`.
- Executes the binary via **ENTRYPOINT**.

### ### docker build / docker images

# 
- Builds image tagged `simple-calculator`.
- Resulting image size shown: **861 MB**.
- Speaker remarks this is excessive for a basic calculator app.
- Notes even switching base images (e.g., UBI minimal) or using a Go image still likely won’t bring it below a few hundred MB in this comparison.

## Demo Part 2: Multi-stage build + distroless via `scratch`

### ### Multi-stage Dockerfile (two stages)

# 

- Stage 1:
- Ubuntu-based build stage
- installs Go
- copies source
- builds the binary
- **no ENTRYPOINT/CMD**
- Stage 2 (final):
- `FROM scratch`
- `COPY --from=build <binary> <binary>`
- `ENTRYPOINT` executes the binary
- Speaker calls `scratch`:
- “minimalistic distroless image” and, in his view, the most minimal option “till date.”
- Important limitation noted:
- This works for Go because no runtime is required.
- For Python/Java, scratch would fail unless you add the runtime; instead use Python/Java distroless images.

### ### docker build (tagging fix)

# 
- Initially forgets `-t` and corrects it.
- Builds `simple-calculator-multi-stage`.

### Result: size comparison (the headline)

# 
- `simple-calculator` (single-stage): **861 MB**
- `simple-calculator-multi-stage` (multi-stage + scratch): **1.83 MB**
- Speaker highlights the magnitude: reduced by “~800 times” (his phrasing).

## How to find distroless images (where they live)

# 
- Suggests searching for the **distroless images GitHub repository**.
- From there:
- choose language folder (example: **Java**)
- in `README.md`, find image locations/tags
- Example guidance:
- For Java, pick appropriate distroless OpenJDK version (e.g., 11, 17) and replace the final stage base image accordingly.
- Mentions he tested Java too:
- Java distroless final image can be ~**200 MB** (bigger than Go because it includes OpenJDK runtime).

## Wrap-up: what to say in interviews

# 
- Key outcomes of multi-stage + distroless:
- reduced image size dramatically
- improved security posture (fewer OS tools/packages → fewer vulnerabilities)
- Closes with like/comment/subscribe reminders.

# 

#