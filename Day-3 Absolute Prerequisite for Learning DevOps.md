- # DevOps in an Organization: Roles, Requirements Flow, SDLC Fit, and Jira Basics

## Why this video (day-to-day DevOps work + where tasks come from)

# 

- Speaker frames a common learner question: if you join as a DevOps engineer, **what are your day-to-day tasks** and **where do requirements come from?**
- What the video will cover (in this order):
1. Different **roles** in an organization (and why DevOps doesn’t get requirements directly from customers)
2. A quick **synopsis** of what each role does
3. A simplified view of **SDLC** (Software Development Life Cycle) and where DevOps fits
4. A practical walk-through of **Jira** (project management tool): account setup, how requirements become tasks, how work is tracked/updated

## Example organization setup used throughout

# 
- Assumption: you join **Amazon** as a DevOps engineer.
- Important clarification: within a company there are multiple **projects/verticals** (e.g., Amazon Fresh, Prime, e-commerce, home services).
- For this video, the chosen project is **Amazon Fresh**, and you are one of multiple DevOps engineers on that project.

## Roles in an organization (top-to-bottom requirement flow)

### Customers (starting point)

# 

- Customers are presented as the most important part of any business.
- Businesses gather customer feedback (good or bad); “most of the times” requirements come from customers (also sometimes driven by market/competitors).
- Example customer requirement:
- “Deliver groceries within **15 minutes** across all zip/pin codes.”

### Business Analyst (BA)

# 
- Key point: DevOps engineers do **not** directly interact with customers/clients.
- BA role:
- Interacts with customers and also interacts with business-side teams (marketing/sales).
- Captures requirements and prepares documentation.
- Document created:
- **BRD** (Business Requirement Document)
- Example BRDs created:
1. **15-minute grocery delivery** service
2. Add a new payment method (e.g., **UPI** or **Stripe**) as a payment gateway

### Product Manager (PM)

# 
- PM prioritizes which requirements to do and when (based on product direction and org capability).
- PM is described as not “completely technical.”
- Example prioritization:
- PM likes the **15-minute delivery** BRD and targets it for **Q1** (Jan–Mar).

### Product Owner (PO)

# 
- PO receives prioritized requirements from PM and goes deeper into details.
- PO breaks requirements into actionable items:
- These are “usually called **epics**” (speaker notes some people may label differently).
- Actionable item examples for the 15-minute delivery feature:
- UI needed
- Backend implementation
- Database setup
- Criteria to mark the requirement “done”
- PO interacts heavily with the solutions/software architect rather than developers most of the time.

### Solutions Architect / Software Architect (SA)

# 
- SA is described as the more technical person in this chain.
- SA prepares:
- **HLD** (High-Level Design)
- **LLD** (Low-Level Design)
- SA may push back on timelines/feasibility (skills/expertise constraints), which can cause PM to de-prioritize items.

### Development team (and where DevOps requirements show up)

# 
- Developers receive the requirement plus HLD/LLD and identify what they need to implement it.
- Examples of needs that trigger DevOps work:
- Infrastructure
- A **Kubernetes cluster**
- **Docker** expertise / Dockerfiles
- Git repositories
- Key takeaway (speaker’s emphasis):
- DevOps engineers often get requirements **indirectly** after the requirement reaches developers (and sometimes via architects too).

### QA / QE team

# 
- QA engineers test while developers build.
- Term used:
- **QE** = Quality Engineers / Quality Assurance Engineers (testing responsibility).

### Database Administrator (DBA)

# 
- DBA may administer the database; speaker notes database setup might be done by DevOps, while administration can be done by DBAs (as described).

### SRE (Site Reliability Engineer)

# 
- After product goes live, SREs focus on:
- reliability and availability
- dashboards, metrics
- notifiers/alerts to notify teams if something goes wrong

### Technical Writer

# 
- Technical writers document features and changes.
- Example used:
- Kubernetes or Android releases—if features aren’t documented, users won’t know what changed or how to use new features.
- May be embedded in a team or shared across multiple scrum teams.

## Oneliner synopsis (speaker’s recap of role responsibilities)

# 
- Customers provide feedback/requirements.
- **Business Analysts** interact with customers and create **BRDs**.
- **Product Managers** define vision and prioritize items (also consider competitors).
- **Product Owners** manage backlog and convert vision into actionable items/stories.
- **Solutions/Software Architects** design technical structure/frameworks via **HLD/LLD**.
- **Scrum team** executes: developers + DevOps + QA/QE + DBA (+ technical writer if needed).
- Work is mainly driven by developers breaking epics into tasks and identifying infra needs that flow to DevOps/QA/etc.

## SDLC (quick fit-in, after role flow)

# 
- Speaker notes SDLC looks simple in diagrams but is complex in reality due to many roles and handoffs.
- SDLC phases listed (in this order):
1. Planning (requirements taken)
2. Analysis (analyze feasibility/priority: do now vs later)
3. Design (HLD/LLD by solutions architect)
4. Implementation (developers + DevOps + QA/QE working)
5. Testing & integration
6. Maintenance (often handled by SREs)

## What DevOps engineers do beyond “requirements from developers”

# 
- DevOps engineers don’t only create infra requested by dev teams.
- They also:
- Identify gaps in the SDLC and reduce cycle time (e.g., “3 months → 2 months”).
- Unify dev + QE efforts via automation.
- Example given:
- QE tests (manual or automated) can be integrated into a **CI/CD pipeline** so tests run automatically when developers make changes.
- DevOps also integrates **security** into the pipeline (as described).
- Note: speaker says it’s okay if you don’t know CI/CD yet; references other videos where it’s explained.

## Why project management tooling is needed (visibility + accountability)

# 
- A blocked DevOps task (e.g., Kubernetes cluster creation blocked due to budget, VPC issues, or lack of expertise) can block developers and therefore the overall requirement.
- Because everyone is interconnected, status must be visible to stakeholders and eventually customers.
- This need for visibility/accountability leads into Jira.

## ### Jira (project management tool) — setup + basic workflow

### Why Jira

# 

- Used to track work end-to-end so management can see:
- who owns what
- what’s blocked
- status/progress across teams
- Speaker notes alternatives exist, but Jira is “very popular” and used widely.

### Creating a free Jira Cloud account (demo steps)

# 
1. Open a new tab and search **“Jira cloud login.”**
2. Sign up with an **Atlassian** account (can use Google account).
3. Create a Jira site (speaker names it **“Amazon demo vala”** since “Amazon” may be taken).
- Notes: in real companies, licensing/account setup is handled by management; this is for demo/practice.
4. Set up a project (speaker chooses **Amazon Fresh**).

### Jira workflow shown (from PO epic → stories → assignments)

# 
1. **Product Owner creates an Epic**
- Work type: Epic
- Epic name example: **“15 minutes delivery service.”**
- PO also defines *acceptance criteria / definition of done*:
- e.g., add feature in mobile app with good UI
- option to disable 15-minute service anytime
- enable 15-minute service in desktop application
2. Epic moves through statuses (as described):
- todo → in progress → in review → done
3. Why this is done:
- Management (VP/director) can check the board and track epic status.

## ### Scrum (within Agile) + sprints (how work is planned)

# 
- Speaker introduces Scrum as a framework within Agile.
- *“Sprints are nothing but two weeks or 3 weeks work tracked as part of sprint.”*
- Sprint rhythm described:
1. **Sprint planning**: plan 2–3 weeks of activities by reviewing backlog
2. Work execution during the sprint
3. **Sprint retrospective**: review how much work was completed vs remaining

## How DevOps engineers receive day-to-day work (Jira stories example)

# 
- During backlog review/sprint planning, epics are broken into smaller **stories**.
- Example developer story created from the epic:
- “Identify the framework required to implement mobile advanced UI” (linked to the epic as its parent).
- How DevOps work emerges:
- A developer says: “I need a Kubernetes cluster.”
- A new story is created: **“create a Kubernetes cluster”** (or via Terraform, as mentioned).
- This story is assigned to a **DevOps engineer**.
- If DevOps has bandwidth:
- picked up in the current sprint
- If not:
- remains in backlog and is taken in a future sprint
- Additional example:
- Developer needs **AWS RDS** → new story created and added to backlog for DevOps.

### Updating progress in Jira (DevOps responsibility)

# 
- DevOps engineers update the Jira ticket regularly:
- add status updates in comments (today’s status, tomorrow’s status, etc.)
- This allows management and the scrum team to track progress.

### Notes on methodologies

# 
- Speaker avoids going deep into Agile variants, but mentions:
- some companies use **Scrum**
- some use **Kanban**
- Core point stays: Jira is used daily to track work and communicate status.

### Suggested learning resource (mentioned)

# 
- Atlassian resource: **“Learn Jira in 5 minutes”** (speaker recommends exploring it).

## Closing

# 
- Speaker says the video should clarify requirement gathering/task allocation and how DevOps work is tracked.
- Invites questions in comments; offers to make another detailed video if needed.

#
