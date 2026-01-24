- # Day 9 (DevOps Zero to Hero): Version Control, Git, and GitHub Basics

## Course context & setup

# 

- Speaker introduces this as **Day 9** of a “complete end-to-end devops course” called **DevOps Zero to Hero**.
- Recommends new viewers watch prior playlist videos to get the “gist” of earlier topics.
- Topic focus: understanding **Git/GitHub** starting from the core concept of **version control**.

## Version Control System (VCS): what it is and why it’s popular

# 
- Speaker frames Git/GitHub as built on the fundamental idea of **version control**.
- *“The fundamental… core concept of git and GitHub is version control system.”*
- VCS addresses **two major problems**:
1. **Sharing of code**
2. **Versioning (history / rollback)**

### Problem 1: Sharing code (why it’s hard in real life)

# 
- Example scenario:
- Two developers (Dev1 and Dev2) working on the same app (a “calculator application”).
- Dev1 writes **addition**, Dev2 writes **subtraction**.
- End goal: combine both into a **common/centralized application**.
- Why “just email/slack” doesn’t scale:
- Real organizations have “hundreds of packages and thousands of files.”
- Example: Dev1 changed **25 files**, Dev2 changed **32 files**, plus dependencies (e.g., “jar files”).
- Sharing becomes “practically impossible” without tooling.

### Problem 2: Versioning (tracking changes over time)

# 
- Example evolution of a requirement:
- Addition of **two numbers** → changed to **three numbers** → changed to **four numbers**
- Later decision: revert back to **two numbers**.
- Need described:
- Ability to return to code from “10 days back… five days back… 20 days… 50 days.”
- Why it’s hard manually:
- Day-by-day changes can span many files (100 files one day, 5 the next, 50 the next).
- VCS helps answer: “what is the change that you have done three days back?”

## Why Git became popular: centralized vs distributed VCS

# 
- Speaker notes there were older tools before Git:
- **CVS**, **SVN**
- Key difference:
- SVN/CVS are *“centralized version controlling systems.”*
- Git is *“a distributed version control system.”*
- Interview framing:
- Common question: difference between **centralized vs distributed** VCS (or SVN vs Git).

### Centralized model (SVN example)

# 
- Dev1 and Dev2 communicate via a **single central server** (SVN).
- Sharing flow:
- Dev1 pushes changes to SVN; Dev2 pulls from SVN.
- Main downside:
- **Single point of failure**—if the central server goes down, Dev1 and Dev2 “can no longer communicate.”

### Distributed model (Git concept)

# 
- In a distributed system, developers can create **multiple copies** of the repository.
- Speaker explanation:
- Developers can “mimic” the central repo by creating copies and sharing between copies, not relying on one server.
- If the main place goes down, developers still have their own full copy.

### Fork (concept + interview question)

# 
- Speaker calls out another interview question: *“what is a fork?”*
- *“Fork is nothing but you create an entire copy of your original source.”*
- Example:
- Organization repo (e.g., “example.com” hosted on a Git repository)
- Individual creates a copy named like “Fork Abhishek”
- Benefit: the entire code is still available even if the original is down.

## Git vs GitHub (conceptual difference)

# 
- Another interview question: *“what is the difference between git and GitHub?”*
- Git:
- *“Git is a distributed version control system.”*
- Open source; organizations can download Git and host it themselves.
- Example described: create an **EC2 instance**, install Git, and developers commit changes to that Git server.
- GitHub (and similar platforms):
- Built “on top of” Git as a usability and collaboration layer.
- Adds features like:
- Better usability/UI
- Raising issues, commenting, peer review
- Project management / tracking
- Similar tools mentioned:
- **GitLab**, **Bitbucket**
- Self-hosted note:
- Self-hosted Git exists but is described as “rare” and involves “a lot of maintenance activity,” so many prefer GitHub/GitLab.

## ### Git CLI installation (speaker’s approach)

# 
- Git is described as a **command line tool** that must be installed.
- Installation path shown:
- Go to the website “git-scm.com/download” (speaker also suggests searching “git download”)
- Choose OS (Mac/Windows/Linux) and distribution-specific install commands.
- Verification:
- Run `git` to see available commands; that indicates installation succeeded.

## ### Creating a local Git repository (demo workflow)

### Create a project folder and file

# 

- Speaker example project:
- Creates directory like `example.com`
- Creates a file `calculator.sh`
- Starts with simple “addition of two numbers” example content.

### Initialize repository

# 
- Command introduced:
- `git init`
- Output meaning:
- *“Initialize the empty git repository.”*

### Confirm repo exists: `.git` folder

# 
- Uses `ls -la` to show a hidden folder:
- `.git`
- Key point:
- Git “tracks everything” using `.git`.
- If `.git` is deleted, the folder is no longer tracked as a repo.

### What’s inside `.git` (high-level)

# 
- Mentioned components:
- **refs** and **objects**
- Speaker: everything is tracked as “objects.”
- **hooks**
- Used to prevent bad commits (example: passwords/API tokens).
- **config**
- For credentials / secure repo settings / secrets / TLS/certificates (as described).
- **HEAD**
- Speaker says it’s not required “right now” but will be explained later.

## ### Core Git lifecycle commands (the “three essential”)

# 
- Speaker highlights three commands everyone should know:
1. `git add`
2. `git commit`
3. `git push`

## ### Tracking files: `git status` → `git add`

### Check current status

# 

- Command:
- `git status`
- Meaning explained:
- Shows “untracked file(s)” (e.g., `calculator.sh`).
- Git is effectively asking whether it should track the file.

### Add file to tracking

# 
- Command:
- `git add <filename>`
- After add:
- `git status` shows no untracked files (speaker focuses on track/untrack first).

## ### Seeing changes: `git status` + `git diff`

### Modify the file (example change)

# 

- Speaker changes addition from:
- `a + b` → `a + b + c`

### Git detects modification

# 
- `git status` shows the file is “modified.”

### View exact changes

# 
- Command:
- `git diff`
- Output interpretation:
- Shows what changed in `calculator.sh` (from `a+b` to `a+b+c`).

## ### Versioning via commits: `git commit` and `git log`

### Create a commit

# 

- Command pattern shown:
- `git commit -m "<message>"`
- Example message:
- “this is my first version of addition”
- After committing:
- `git status` shows “working tree clean.”
- Also shows branch name: “on branch main” (speaker says branch will be explained later).

### Add a new change and commit again

# 
- Example new feature:
- Adds subtraction line like `a - b`
- Flow repeated:
1. `git status` (shows modified)
2. `git diff` (review changes)
3. `git add calculator.sh`
4. `git status` (changes staged; suggests commit)
5. `git commit -m "this is my second version"`

### View commit history

# 
- Command:
- `git log`
- Speaker shows it lists commits with:
- commit IDs
- author
- commit messages (“first commit”, “second version”)

## ### Going back to a previous version: `git reset --hard`

# 
- Scenario:
- Product owner asks to revert to a previous version.
- Process described:
- Use `git log` to find the earlier commit ID.
- Command shown:
- `git reset --hard <commit-id>`
- Verification:
- `cat calculator.sh` shows file reverted (only the earlier line remains).
- Speaker note:
- There are multiple ways to do rollback; this is “one basic command” used to explain the concept.

## Sharing code (why GitHub/self-hosted/Bitbucket matter)

# 
- Speaker summarizes what was covered so far as **local versioning only**:
- init, add/track, commit, diff, log, reset
- To share with peers/org:
- Need a remote distributed place: **GitHub** (or self-hosted Git, Bitbucket, GitLab).
- Reason given:
- Local repo is only on “your personal laptop”; remote enables collaboration and distribution.

## ### GitHub: account creation + creating a repository

### Create a GitHub account

# 

- Steps described:
- Go to `github.com`
- Click “Sign up”
- Provide email, answer questions, account gets created.

### Create a new repository

# 
- From GitHub UI:
- Click “New”
- Choose:
- Repository name (example given like a shell example project)
- Description/details
- Public vs Private:
- Public: share with anyone
- Private: only people granted access
- Optional: initialize with a **README**
- README described as metadata/instructions (e.g., what the calculator does)
- Click “Create repository”

### Forking on GitHub

# 
- Speaker points out GitHub has a **Fork** option:
- You can create a copy and collaborate using forks.

## Wrap-up & what’s next

# 
- Today’s coverage: “concept of git and GitHub,” with Git basics demonstrated in terminal.
- Tomorrow’s plan: deep dive on GitHub:
- Why it’s popular vs GitLab/Bitbucket/self-hosted
- Users and organization management
- Issues, pull requests
- GitHub CI/CD (high-level demo)
- Project management and security features
- Speaker also mentions future sessions may cover:
- More Git commands
- Common interview questions
- Closing requests:
- Post questions in comments
- Share the channel with friends/colleagues

## -
