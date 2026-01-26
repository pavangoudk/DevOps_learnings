# DevOps Day 25: Containerize a Django Web App with Docker

## Intro: how this builds on Days 23–24

# 

- Speaker introduces **Day 25** of the complete DevOps course.
- Goal: **deploy the first Django web application as a Docker container**.
- Recap of prerequisites (Days 23–24), and why to watch them first:
- Containers basics: why containers are lightweight vs virtual machines (VMs).
- What files/folders a container has; what it uses from the kernel; how containers are isolated.
- Docker architecture and Docker lifecycle.
- Docker terminology (Docker daemon, Docker Desktop, Docker registry, etc.).
- Installing Docker on an EC2 instance, plus common permission issues.
- Running a Docker command-line (CLI) app.

## Why this Django example matters (vs “hello world”)

# 
- This Django app is “slightly heavy / slightly tricky” compared to a simple CLI hello-world container.
- The app itself is “very basic” (single-page), but the **containerization process** is the key:
- As a DevOps engineer, whether it’s 1 page or 3 pages doesn’t change the core containerization steps.

## Do you need programming experience?

# 
- Speaker addresses a common question from comments.
- Guidance:
- You don’t need to be able to build a full Django app, but you should understand **how an application functions** and “at least” the skeleton/workflow.
- You should be able to look at a project and identify:
- where config lives (e.g., `settings.py`)
- where behavior lives (e.g., `views.py`)
- where templates are rendered from (e.g., templates folder)

## Django application workflow (5-minute overview before containerizing)

### ### pip (installing Django)

# 

- Step 1: install **Python**, then install **Django** using **pip**.
- Mentions verifying install by importing Django (e.g., `import django`).

### ### Django admin (project scaffolding)

# 
- Django provides **Django admin**, described by analogy:
- “very similar to your Ansible Galaxy,” which creates skeletons (roles in Ansible; projects in Django).
- Create project skeleton with:
- `django-admin startproject <project-name>`
- In his case: `django-admin startproject devops`

### Project configuration files

# 
- Inside the project folder (`devops/devops`), key files described:
- **`settings.py`**: *“entire configurations for your Django project”* including:
- IP allow/whitelist
- database configuration
- secret keys / secure info
- middleware
- templates support
- WSGI-related settings (speaker mentions “web server gateway interface”)
- **`urls.py`**: responsible for routing/serving content.
- Example URL shown: `<instance-ip>:<port>/demo`
- Explains `/demo` is the context root handled by the demo application.
- Mentions `/admin` as another path.

### Create the app inside the project

# 
- After creating the project, you still need an app.
- Command described:
- `python manage.py startapp <app-name>`
- In his case: app name is **`demo`**
- The app creation generates multiple files automatically (templates folder is added manually, explained next).

### views + templates

# 
- **`views.py`**: where you write Python logic.
- His example: a simple function `index` that *renders an HTML file*.
- Templates go in a **`templates`** folder; content served to the browser is rendered from there.

## The pre-container pain Docker solves (dev vs QA mismatch)

# 
- “Before containers,” QA used to:
- download repo/artifacts
- install dependencies from `requirements.txt`
- face OS differences (Windows vs Mac vs Linux distro) where commands may not work
- Classic conflict described:
- developer: “application works fine”
- QA: “application not working”
- unclear whether it’s CI/CD, environment, or user setup
- Docker solves this by bundling dependencies so the end user only needs:
- **Docker build**
- **Docker run**
- If Docker is installed, the OS matters less (speaker ties this back to container bundling + host kernel usage).

## DevOps engineer responsibility: containerize the Django app

# 
- Typical prompt (example): “Here’s my Django application—containerize it.”
- DevOps engineer should analyze workflow (with developers if needed), but ideally already understands common app patterns (Python/Django, Node.js, etc.).

## Writing the Dockerfile for the Django app (what he did)

### ### Base image selection

# 

- Chooses **Ubuntu** as the base image.
- Notes you could use **CentOS**, but then you must swap package manager commands (apt → yum).

### ### WORKDIR (standardization)

# 
- Uses a working directory (e.g., `/app`) as a team/org standard for placing source code.

### ### requirements.txt first (Python dependencies)

# 
- Key DevOps insight: copy **`requirements.txt`** first because it contains dependencies needed to run the app.
- Requirements mentioned: **Django** and **tzdata**.

### ### Copy source code

# 
- Copies the Django project source after copying requirements.

### ### Install Python + pip install dependencies

# 
- Ubuntu base image doesn’t include Python by default (in his flow), so he installs Python.
- Installs dependencies using:
- `pip install -r requirements.txt`

## ENTRYPOINT vs CMD (explicit Q&A)

# 
- Speaker answers a common comment question.

### Definition (as described)

# 
- *Both ENTRYPOINT and CMD can serve as the “start command” when someone runs `docker run`.*

### Key difference (speaker’s emphasis)

# 
- *ENTRYPOINT is “something that you cannot change” (non-overridable by normal container runs).*
- *CMD is configurable/overridable (e.g., change IP/port or parameters).*

### Example reasoning (Django runserver)

# 
- Keeps **`python3`** as the non-changeable executable in **ENTRYPOINT**:
- He doesn’t want users to swap `python3` to something else like `node.js`.
- Keeps the run parameters in **CMD** so users can change them:
- Example: changing port if **8000 is already occupied** on someone’s machine.
- If everything were in ENTRYPOINT, port conflicts would be harder to resolve without overrides.

## Demo: build and run the Django container

### ### Git + repository layout

# 

- Repo is cloned already on his EC2 instance.
- Two example folders mentioned:
- a prior “first Dockerfile” CLI example (hello-world-like)
- the Django app folder (the one used today)

### ### docker build

# 
- Runs `docker build .`
- Confirms image exists via:
- `docker images`

### ### docker run (first attempt: why it doesn’t work)

# 
- Runs container interactively:
- `docker run -it ...`
- App still won’t load in the browser because:
- the container’s port isn’t mapped to the host
- Django is running inside the container (port 8000), but he’s trying to access via the EC2 host IP

### ### Port mapping (fix)

# 
- Uses port mapping:
- `docker run -p 8000:8000 ...`
- After mapping, the Django app becomes accessible in the browser.
- Speaker emphasizes how easy it becomes once Dockerfile is correct.

## AWS-specific gotcha: Security Group inbound rules

# 
- If someone follows steps but app still doesn’t work:
- likely missing inbound rule for port **8000** on the EC2 **Security Group**
- Steps shown conceptually:
1. EC2 instance → **Security** tab
2. Open the **Security Group**
3. Edit inbound rules
4. Add rule:
- type: custom TCP
- port: 8000
- source: everywhere (or restricted to your laptop IP)

## What’s next (tomorrow’s class)

# 
- Next topics teased (present in repo but deferred to avoid overload):
- Docker commands (deeper)
- Docker networking
- Multi-stage Docker builds
- Reducing container image size

## Closing + question for comments

# 
- Out-of-syllabus question for viewers:
- “How many EC2 instances can you run on a free AWS account?”
- Asks for answer plus explanation (1, 2, 5, 100—how?).
- Standard wrap-up: like, comment, ask questions, share with friends/colleagues.

#