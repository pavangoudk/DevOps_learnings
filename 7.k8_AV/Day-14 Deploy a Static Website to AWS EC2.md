# Deploy a Static Website to AWS EC2 (GitHub → Apache on RHEL) — Live Hands-On

## Intro: returning guest + session goal

# 

- Abhishek introduces **Varun** (previously discussed internships and career topics).
- Varun sets today’s goal:
- *a live demo / hands-on project* to “sum up” what viewers have learned (GitHub, cloud computing, virtual servers, websites).
- deploy a website “within few clicks,” highlighting “scalable and reliable” cloud services.
- Abhishek reacts positively to “scalability” + “few clicks” and invites Varun to start.

## Varun shows the website locally first

# 
- Varun shares screen and shows a folder containing an `index.html`.
- Describes the demo site as a **static website** stored on his local system.
- Mentions it has multiple pages/sections (e.g., clicking “intro” opens another page).

## Plan: GitHub → AWS server → host site

# 
- Varun states the workflow:
1. Push the website files to **GitHub**
2. Launch a server on **AWS**
3. Deploy and host the site from the server

## Create a GitHub repository

### ### GitHub

# 

- Goes to GitHub and creates a new repository:
- example name: `AWS demo`
- adds a `README.md`
- Notes viewers are likely familiar with the steps.

## Push local website files to GitHub (terminal)

### ### Terminal (Hyper terminal)

# 

- Navigates to the folder where static content exists using basic Linux commands:
- `cd` into `Documents`, then into the website folder.

### ### Git (basic workflow)

# 
- Explains why initialize Git:
- *an empty Git repository tracks changes* in the filesystem.
- Runs standard commands:
1. `git init` (he sees “reinitialized” because a `.git` existed earlier)
2. `git add .`
3. `git commit -m "added all the files"` (message example)
4. `git remote add origin <repo-url>`
5. `git push -u origin main` (attempted)

#### Demo hiccup (and fix)

# 
- Push fails due to existing Git history / remote already set.
- Abhishek reassures: *“demos are meant to fail… it’s always a good learning.”*
- Varun fixes by avoiding copying the `.git` folder:
- copies only website content into a clean folder and redoes the steps.
- He pushes to **master** instead (mentions “main branch is protected by default” in GitHub for final work, so he uses master for the demo).
- Verifies on GitHub: files/assets appear in the repo.

## Move to AWS: create an EC2 instance

### ### AWS (Amazon Web Services)

# 

- Opens AWS site (`aws.amazon.in` mentioned).
- Notes:
- account setup requires credit card verification
- AWS offers free-tier services/credits for new users (mentions “free for one year”)
- Chooses region:
- **Asia Pacific → Mumbai**
- Rationale: host near the audience to reduce latency (“low latency solutions”).

### ### EC2 / Instances

# 
- Explains “instances” simply:
- *an instance is basically a computer.*

## Choose OS image (AMI) and instance type

# 
- Explains OS image purpose:

- you need an operating system to launch a computer (mediates between user and hardware).
- Selects an OS:

- chooses **Red Hat Enterprise Linux (RHEL)** (says it’s “nice and clean” for Linux beginners).
- chooses **free-tier eligible** image for demo.
- Instance type:

- uses **t2.micro** (mentions: 1 vCPU, 1 GB memory).
- highlights scalability by pointing out much larger instance types exist (up to very high CPU/RAM).

## Key pair for secure login

### ### SSH key pair (.pem / .ppk)

# 

- Explains why key pairs:
- passwords can be insecure; AWS prefers key-based login.
- Creates a new key pair:
- gives it a name like “demo website”
- Formats:
- `.ppk` for PuTTY
- `.pem` for OpenSSH
- Downloads the key file and emphasizes:
- keep it safe; it’s required to log in
- you can reuse one key pair for multiple EC2 instances

## Networking settings: VPC, public IP, and security group

### ### VPC (Virtual Private Cloud)

# 

- Varun defines VPC conceptually:
- *a virtual private network where only you (and those you allow) can enter.*
- Abhishek clarifies a common confusion:
- *VPC ≠ VPN.*
- *VPC is for isolating/secure cloud resources; VPN is a secure tunnel for data transfer.*

### Public IP (important for a public website)

# 
- Varun says:
- keep **Auto-assign public IP = Enabled** for this demo,
- otherwise the instance won’t be accessible from the public internet.
- Gives an example where you’d disable public IP:
- a database server storing sensitive info (e.g., salary details) should not be internet-accessible.
- Mentions instances can have:
- public IP (internet-facing)
- private IP (internal access)

### ### Security group / Firewall

# 
- Varun explains firewall with analogy:

- *a security guard for your bungalow* deciding who can enter.
- SSH rule:

- allow **SSH** (Secure Shell) so you can log in remotely.
- notes you can restrict SSH to “my IP” for better security.
- Abhishek adds:

- even if the private key is compromised, restricting SSH to your IP blocks attackers from other IPs.
- Varun shows checking “What is my IP” via Google and matching the IP AWS detects.
- HTTP rule:

- must allow **HTTP** traffic to serve the website publicly.
- AWS warning:

- AWS flags that allowing SSH from “Anywhere” is risky and recommends tightening it.

#### HTTP vs HTTPS note (Abhishek)

# 
- Abhishek points out:
- HTTP is insecure; real-world setups typically use **HTTPS**.
- common approach: redirect HTTP → HTTPS at the **load balancer**.

## Storage settings (EBS)

### ### EBS (Elastic Block Store)

# 

- Mentions free-tier storage allowance (e.g., up to 30 GB eligible).
- Sets storage (example: 10 GB SSD) and moves on (deep dive deferred).

## IAM mention in advanced details

### ### IAM (Identity and Access Management)

# 

- Varun defines IAM:
- *create users/roles with limited permissions instead of giving full AWS account access.*
- Example:
- in a large org with many teams/servers, new joiners should get access only to what they need (read-only / write / execute, etc.).

## Launch instance (“few clicks”)

# 
- Launches the instance and notes how quickly it becomes running.
- Abhishek shares an observation:
- GCP sometimes appears faster to reach running state because it may allocate from a pre-warmed pool of instances.

## Connect to EC2 using SSH

### ### SSH (client connection)

# 

- In AWS console:
- clicks **Connect** → chooses **SSH client** instructions.
- On local terminal:
1. navigates to where the key file is (Downloads)
2. runs permission hardening:
- `chmod 400 demo-website.pem` (he explains it prevents public readability)
3. runs SSH command (explains parts):
- `ssh -i <key.pem> ec2-user@<public-ip>`
- `-i` specifies identity file
- default username is **ec2-user**
- Types `yes` to trust the host and logs in.
- Confirms prompt changes to show he’s on the remote EC2 instance.

## Become root and update packages

### ### sudo / root

# 

- Attempts update and gets “not root” error.
- Explains:
- `root` is the superuser with special privileges.
- Uses:
- `sudo su -` to become root
- Runs:
- `yum update -y`
- explains `-y` auto-answers “yes”.

## Install Git on the EC2 instance

### ### yum + git

# 

- Notes Git isn’t installed by default.
- Installs Git (via yum) and confirms it’s available.

## Pull website files from GitHub onto EC2

### ### git clone / branches

# 

- Explains why Git is used:
- you can’t drag-and-drop files into a cloud server; you don’t have physical access.
- Runs:
- `git clone <repo-url>`
- Notices only `README.md` at first because default branch is different.
- Checks and switches branches:
- `git branch` (sees current branch)
- `git checkout master`
- After switching, confirms the full website content appears (index/assets/errors/images, etc.).

## Install Apache and deploy site files

### ### Apache httpd

# 

- Installs Apache:
- `yum install httpd`
- Key hosting directory:
- Apache serves content from:
- `/var/www/html`
- He emphasizes: *files must be placed here or Apache won’t host them.*

### ### Linux file operations (cp, -r, ls, pwd)

# 
- Copies files into Apache web root:
- copies `index.html` to `/var/www/html`
- tries copying folders and hits an error; explains:
- `cp` copies files; for directories use recursive:
- `cp -r <dir> /var/www/html`
- repeats for folders like `assets`, `errors`, `images`.
- Verifies:
- `ls` to list contents
- `pwd` to confirm current directory is `/var/www/html`

## Start the web server and verify access

### ### systemctl

# 

- Tries opening public IP in browser; gets error (Safari/Chrome).
- Identifies missing step:
- Apache isn’t started.
- Starts service:
- `systemctl start httpd`
- Mentions a check command (`chkconfig` / service enablement is referenced during output).
- Refreshes browser:
- website loads and is publicly accessible via the EC2 public IP.
- Notes a common issue:
- browsers may default to **https://**
- since only HTTP is configured, ensure URL is **http://** (remove the “s”).

## Wrap-up and extensions

# 
- Abhishek praises the explanation as an “absolute beginner’s guide.”
- Varun suggests next step:
- install Node.js (`yum install nodejs`) to host dynamic sites (Node/Express).
- Both suggest progression:
- start with static website hosting → move to dynamic apps.
- advanced users can containerize (“dockerize”) and explore more.
- Advice for beginners:
- you don’t need to be a web dev expert; even a simple HTML page is enough.
- can use sample HTML from W3Schools for practice.
- Varun offers LinkedIn contact for questions; Abhishek says they’ll reply in comments and invites feedback/sharing.

# - -

