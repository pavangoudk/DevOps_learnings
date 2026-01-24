# Day 11 DevOps Course: Essential Git Commands (Workflow, Remotes, Clone vs Fork, Branching, Merge vs Rebase)

## Opening context (why Day 11, what’s covered)

# 

- Speaker apologizes for not posting the previous day due to personal reasons.
- This is **Day 11** of a 45-day DevOps course; prior days (0–10) covered:
- DevOps fundamentals
- Linux scripting
- Git basics
- Today’s focus: **Git commands useful for DevOps engineers**, and generally useful for software engineers day-to-day.
- Setup for demo:
- Creates a blank folder named “git demo” (described as empty at the start).

## Starting example (calculator task) + approach

# 
- Uses the recurring example: write a simple calculator script (`calculator.sh`), starting with an addition function.
- Speaker intent: demonstrate “most of the things through command line” and also show how the same is done via **UI**, using GitHub as the example.

## ### GitHub UI method (creating a repository)

# 
- UI approach to create a repo:
1. Click **New**
2. Provide repository name + description
3. Choose **public** vs **private**
4. Click **Create repository**
- Speaker notes UI is straightforward because it involves no commands.

## ### Git CLI method (local repository creation)

### Initialize a local repo

# 

- Command used: `git init`
- Definition (speaker phrasing, closely preserved):
- *`git init` “will initialize a local repository for you.”*
- Interview-style Q&A called out:
- “How to create/initialize a Git repository?” → use `git init`.

### Installing Git (speaker’s reference)

# 
- Mentions installing Git from the “git-scm download” page (and notes the current version he’s using, “2.39”).
- Verifying installation:
- Run `git` to see available commands (implied as a validation step).

### What changes after `git init`

# 
- Uses `ls -a` / `ls -ltr -a` to show a new hidden folder appears: `.git`.
- Definition (speaker intent, closely preserved):
- *“.git is the key component of git… responsible for tracking, logging, and more.”*
- `.git` mentioned uses:
- Tracking and logging
- Storing credentials/config for a “secret” repo (as described)
- Preventing secrets from being committed via hooks:
- Example: a developer might accidentally push a password.
- Mentions `hooks` and specifically a **pre-commit hook** to prevent committing secrets.

## ### `git status` (what’s happening in the repo)

# 
- Command used: `git status`
- What it shows initially:
- “Untracked files” (Git doesn’t yet track `calculator.sh`).
- Speaker ties this to Git’s purpose: tracking/version control to recover from accidental deletions or unwanted edits.

## ### `git add` (start tracking files / stage changes)

# 
- Command used: `git add calculator.sh`
- Result via `git status`:
- Shows “changes to be committed” and recognizes `calculator.sh` as a new tracked file.
- Definition (speaker intent, closely preserved):
- *“The very first command… whenever you make any changes… is `git add`” so Git “will track these changes.”*
- Also mentions:
- `git add .` stages “a lot of files at once.”
- Why this matters:
- If you don’t add files, Git won’t track them, so it won’t help you recover or manage changes.

## ### `git diff` (review what changed)

# 
- Command used: `git diff`
- Used to see the exact modifications made (e.g., adding a line like `x = 1 + 2` in the script).
- Speaker positions this as helpful when you’ve made many changes and forgot what you changed.

## ### `git checkout <file>` (discard changes)

# 
- Command mentioned: `git checkout calculator.sh`
- Speaker references it as a way to remove changes from a file (mentioned while explaining add/diff, but says he’ll go slow and focus on `git add` first).

## ### `git commit` (create version history checkpoints)

# 
- Command used: `git commit -m "my first commit"` (message example)
- Definition (speaker intent, closely preserved):
- *Commit exists so you have “a mechanism to see a list of history… what file changes are done,” who did them, and “always go back to a specific change.”*
- `git log` is introduced immediately after committing.

## ### `git log` (view commit history)

# 
- Command used: `git log`
- Speaker explains it helps identify:
- Who made a change (author)
- What the change was (commit message)
- Supports rollback when a change breaks things (e.g., “remove subtraction, keep addition”).

## ### `git push` (send local commits to a remote)

# 
- Definition (speaker intent, closely preserved):
- *`git push` is how you “push your entire code to your git repository”… so teammates can access it in a distributed place (GitHub/Bitbucket/etc.).*
- Interview question highlighted:
- “What is the git workflow you use in your organization?”
- Answer format shown as a chained workflow:
- `git add` && `git commit -m "<message>"` && `git push`

### Why `git push` might “do nothing”

# 
- Speaker demonstrates the failure case:
- If you created the repo locally via CLI, it may have **no remote configured**, so `git push` doesn’t know where to push.
- This is framed as a frequent learner issue in comments.

## ### Remotes (`git remote -v`) + adding a remote

# 
- Command used to inspect remotes: `git remote -v`
- Speaker contrasts:
- A cloned repo (example: Argo CD) has a remote configured.
- The locally initialized “git demo” repo has **no remote**, so pushes don’t work.
- Command mentioned to add a remote: `git remote add ...`
- Speaker does not execute it fully (doesn’t want to create a stale repo), but explains that adding the remote location enables push to a remote repository.

## ### `git clone` (download/pull code locally)

# 
- Speaker introduces cloning as the standard organization workflow:
- Repos often already exist; you clone them locally, then do add/commit/push.
- Definition (speaker phrasing, closely preserved):
- *“`git clone` is a process using which you pull the code from GitHub… or any git repository.”*
- In GitHub UI:
- Click **Code** to find clone options.

### Clone methods mentioned

# 
- HTTPS
- SSH
- GitHub CLI (explicitly says to ignore GitHub CLI “for now”)

## ### HTTPS vs SSH authentication for cloning

# 
- HTTPS:
- Authenticate using your “GitHub password” (speaker’s wording).
- SSH:
- Definition (speaker phrasing, closely preserved):
- *“When you’re using SSH you basically authenticate using your public key whereas… HTTPS… using your password.”*

## ### SSH keys (`ssh-keygen`) + adding SSH key to GitHub

# 
- Command mentioned to generate keys: `ssh-keygen -t rsa`
- Speaker notes:
- On a Linux machine you may already have keys; if not, generate them.
- Keys live under `~/.ssh/`.
- Mentions files typically present:
- Public key
- Private key
- `known_hosts`
- Workflow to use SSH with GitHub:
1. Open/copy the contents of the public key file (example given: `id_rsa.pub`)
- Suggests using `cat` or opening in an editor.
2. In GitHub, go to **Settings** → **SSH keys**
3. Add a new SSH key (name it and paste the public key)
4. After this, cloning/pulling won’t ask for password (as described)

## ### Clone vs Fork (common interview question)

# 
- Clone:
- Download a repository locally.
- Fork:
- Definition (speaker intent, closely preserved):
- *Forking is creating “a copy of this entire code”… because Git is a “distributed version control system.”*
- Example: Argo CD
- Mentions thousands of forks exist.
- A fork is your independent copy; it won’t automatically receive upstream updates unless you do it explicitly.
- After forking:
- You and others can collaborate on your forked repo independently of the original.

## Summary checkpoint (speaker’s “what we covered till now”)

# 
- Three areas summarized:
1. Creating a repository from CLI
2. Difference between clone and fork
3. Git lifecycle/workflow (add → commit → push), plus related setup like remotes and cloning

## Branching refresher + why branches are used

# 
- Notes repos typically start with one branch: `main` (whether created via UI or CLI).
- Why create branches (real-world examples):
- Calculator enhancements: large features shouldn’t be developed directly on `main`.
- Large-org example: Amazon adding a big feature (e.g., “carpenter” / “house services”).
- Core reason (speaker intent, closely preserved):
- *Use branches so big/long-running changes don’t “disturb the existing functionality” delivered to customers.*

## ### Creating and switching branches (`git checkout -b`, `git branch`, `git checkout`)

# 
- Before branching demo, he adds subtraction, then:
- `git add calculator.sh`
- `git diff` (to review changes)
- `git commit -m "my second commit"`
- `git log` (shows first + second commits)

### Create a new branch from current point

# 
- Command used: `git checkout -b division`
- What it does (speaker intent, closely preserved):
- *Creates a new branch “from that point” (current main branch state).*
- Verify branches:
- `git branch` shows `main` and `division`.

### Add a change in the new branch

# 
- In `division` branch:
- Add “division functionality” (conceptually)
- `git add calculator.sh`
- `git commit -m "add division"`
- `git log` shows the new commit on that branch.

### Switch back to main and observe isolation

# 
- Command used: `git checkout main`
- Observation:
- `git log` on `main` does **not** show the division commit yet.
- Speaker takeaway:
- *Branching “separate[s] the development activities.”*

## Methods to move changes between branches: merge, rebase, cherry-pick

# 
- Speaker introduces three approaches:
1. `git merge`
2. `git rebase`
3. `git cherry-pick`

## ### `git cherry-pick` (pick specific commits)

# 
- Definition (speaker intent, closely preserved):
- *Cherry-picking means you are “picking the commits.”*
- Shows how to find commits without switching branches:
- `git log division` (view division branch history from current branch)
- Workflow:
1. Copy commit ID from `git log division`
2. Run `git cherry-pick <commit-id>` on `main`
3. `git log` now shows “add division” on `main`

### Why not always cherry-pick?

# 
- It’s easy for “one or two commits.”
- Not practical for very large feature branches with huge numbers of commits (speaker uses Amazon-scale example).
- Also mentions merge conflicts can arise, making manual cherry-picking unrealistic.

## ### `git merge` vs `git rebase` (why both exist)

# 
- Speaker sets up a practical comparison because many people read about it but don’t understand it.

### Setup branches for comparison

# 
1. Create a merge demo branch:
- `git checkout -b merge-example`
- Add “multiplication” functionality
- `git add`, `git commit -m "demonstrate merge"`
2. Go back to main and create rebase demo branch:
- `git checkout main`
- `git checkout -b rebase-example`
- Add “percentage” functionality
- `git add`, `git commit -m "percentage"`

### Shorter commit view

# 
- Command introduced: `git log --oneline`
- Used to compare histories across:
- `main`
- `merge-example`
- `rebase-example`

### Simulate ongoing main-branch work

# 
- Switch to main and create another commit (“test commit”) to represent other developers continuing work on main while feature branches are in progress.

## ### `git merge <branch>` (bringing changes into main)

# 
- Attempts `git merge merge-example`
- Encounters a situation where Git asks to commit changes before merging (speaker resolves by committing).
- After successful merge:
- `git log` shows merge-example changes applied on top of main’s recent commits (as presented in his log walkthrough).

## ### `git rebase <branch>` (bringing changes with linear history)

# 
- Runs `git rebase rebase-example`
- Encounters a **merge conflict** because “multiple people in different branches are updating the same file.”

## Merge conflict handling (practical)

# 
- Conflict appears in `calculator.sh` with conflict markers.
- How to fix (speaker’s guidance):
- *“Go to that file, sit with the developers… ask… which code has to be taken.”*
- In his demo:
- Conflict was between “percentage” and “multiplication.”
- He resolves by keeping both (removes conflict markers).

### After resolving conflicts

# 
- He stages the resolved file:
- `git add`
- Then continues the rebase:
- Mentions `git rebase --continue` as an option (and indicates you can provide/accept commit messaging during this process).

## What changed in history: key difference between merge and rebase

# 
- Speaker summarizes with both logs and a diagram:
- Both **merge** and **rebase** combine branches.
- The difference is **how commits are organized**.

### `git merge` outcome

# 
- Merge brings branch commits “at the top” (after the current main commits).
- Speaker explains this can make history feel non-linear for someone trying to track “which commit came after which.”

### `git rebase` outcome

# 
- Rebase produces a “linear commit history.”
- Changes from the rebased branch appear before later main-branch commits, making it easier to follow a straight sequence.

### Practical rule-of-thumb (as stated)

# 
- If you want a **linear** history to track commit order clearly, use **rebase**.
- If you’re “not bothered about it,” you can use **merge**.

## Wrap-up: what commands matter day-to-day + interview angle

# 
- Speaker says these are the Git commands used most often day-to-day; other commands are used occasionally and can be searched when needed.
- Mentions he has a separate video on Git interview questions and intends to add it in the description (no details provided in transcript).
- Closing:
- Invite questions in comments
- Like/subscribe/share
-
