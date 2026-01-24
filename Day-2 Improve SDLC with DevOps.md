- # DevOps Zero to Hero (Day 2): SDLC Basics + Where DevOps Fits

## Opening + recap of Day 1

# 

- Speaker thanks viewers for watching Day 1 (“Introduction of DevOps”).
- Recommends watching Day 1 if not already; mentions the **DevOps Zero to Hero playlist** (Day 0, Day 1, etc.).
- Quick recap of Day 1 topics:
- What is DevOps
- Why DevOps
- How to introduce yourself in a DevOps interview

## Why learn Software Development Life Cycle (SDLC)?

# 
- Today is **Day 2**: focus is **Software Development Life Cycle**.
- Speaker says everyone should learn SDLC regardless of role:
- developer, tester, DevOps, or any software/IT role
- Introduces the acronym:
- SDLC = **Software Development Life Cycle**
- *“The software development life cycle in short is also called as SDLC.”*
- Why it matters:
- Organizations (startup, MNC, unicorn) follow **industry standards**.
- Understanding these standards is necessary to work effectively in an organization.

## ### SDLC (Software Development Life Cycle)

# 
- High-level definition:
- *SDLC is “a set of standard that is followed in the software industry to design, develop and to test.”*
- Speaker emphasizes the goal:
- Deliver a **high-quality product**
- Without testing: you may design + develop, but quality can’t be assured.
- One-line summary (speaker framing):
- SDLC is the process followed to **design, develop, and test** in order to deliver a **high-quality product**.

## Example context used: “example.com” e-commerce feature

# 
- Uses a fictional org: **example.com** (can be thought of like Amazon/Flipkart).
- Example feature request:
- Add a **kids catalog** (site previously sold only men/women clothing).
- Speaker says each feature follows the SDLC “cycle” end-to-end.

## SDLC phases (shown as a cycle)

# 
Speaker lays out SDLC as a repeating loop for every new feature:

1. **Planning**
2. **Design**
3. **Building**
4. **Testing**
5. **Deploy**

- Notes it’s drawn as a **circle** because the same process repeats for each new feature.

## Deep dive into phases (in the order presented)

### 1) Planning + Requirements

# 

- Called out as a “fundamental” starting stage.
- Owned by roles like:
- product owner, business analyst, and sometimes senior members
- What happens here:
- Gather customer feedback and validate demand.
- If customers aren’t interested (kids catalog example), you can **stop the idea early**.
- Requirements detail example:
- Determine which age ranges customers want (e.g., 6–12 vs 1–4).
- Quantify interest (speaker uses “10 customers today” and “15–20 potential customers” as an illustration).
- Output of this stage:
- Consolidated requirements (“collective information”).

### 2) Defining requirements (documentation)

# 
- After planning/research, requirements are documented.
- Document named:
- **Software Requirement Specification (SRS)** document
- What it contains (as described):
- Data collected during planning (customer research results, preferences, etc.).

### 3) Designing (HLD and LLD)

# 
- Speaker calls design a “very critical phase.”
- Two design levels:
- **High Level Design (HLD)**
- **Low Level Design (LLD)**

#### High Level Design (HLD)

# 
- Created by: architect, team lead, or senior resource.
- Focus areas described:
- scalability (e.g., higher load during Christmas)
- high availability
- broad architecture choices (e.g., database choice, number of replicas)

#### Low Level Design (LLD)

# 
- Created by: architect or senior development members.
- Focus areas described:
- detailed module/function-level design
- language-level implementation guidance (speaker mentions Java/Python as examples)
- specifics like database calls and function behavior (inputs/arguments and responses)

## Where DevOps becomes “most important” in SDLC (speaker’s focus)

# 
- Speaker transitions: DevOps can be involved earlier “out of interest,” but the “actual importance” is strongest in:
- **Building**
- **Testing**
- **Deployment**
- Key point:
- *DevOps engineers “automate these processes.”*
- Clarification:
- Not saying DevOps shouldn’t be involved in planning/definition/design—but primary focus is the last three stages.

## The last three phases explained (and mapped to roles)

### 4) Building (Developing)

# 

- Speaker equates building with development:
- *“Building is nothing but developing.”*
- Inputs to developers:
- design documents + requirements + “stories” (mentioned)
- Developer workflow described:
- Write code in the organization’s language
- Read items from **Jira** and/or prepared documents
- Get code reviewed by peers
- Push code to a shared source code repository

### ### Jira (mentioned as developer input)

# 
- Developers “read the Jira items” before writing code.

### ### Git (source code repository concept)

# 
- Speaker notes most orgs use **Git**.
- For this lesson, Git is framed simply as:
- a repository that stores and shares code across the team (centralized/distributed mentioned)
- Reason it’s needed:
- Code can’t remain only on one developer’s laptop; it must be shared.

### 5) Testing

# 
- Reason for testing:
- “Works on my server” isn’t trusted as final proof.
- Need “prompt and quality code” for customers.
- Testing flow described:
- Code from Git repo is deployed to a server
- A testing team validates it

### ### QE (Quality Assurance Engineers)

# 
- QE team is defined:
- *“QE team… they are the quality assurance engineers.”*
- Role mapping:
- Developers build; QE tests.

### 6) Deployment

# 
- Deployment described as:
- promoting the application to **production**
- Environment progression example:
- testing occurs on a server like staging/development
- final delivery is to production where customers receive it

## How DevOps fits into SDLC (efficiency + automation)

# 
- Speaker answers: “Where is DevOps coming into picture?”
- DevOps purpose here:
- “fastens the process” and improves delivery speed
- Core mechanism:
- *DevOps engineer “would automate the process… everything happens in an automated way.”*
- Speaker explicitly frames DevOps responsibility as:
- automating **building, testing, and deployment**
- reducing time by “avoiding manual intervention”
- Clarifies DevOps is not manually doing each task:
- DevOps doesn’t personally build/test/deploy each release
- DevOps writes automation/scripts so the flow happens automatically

## SDLC + project management models (high level mention)

# 
- Speaker notes SDLC can be executed under different models:
- **Waterfall**, **Iterative**, **Agile**
- Emphasis:
- “Most organizations these days” use **Agile**
- Agile described at a high level:
- You don’t wait for all planning/design to finish for everything
- You work in “short sprints” and repeat the SDLC loop in smaller chunks
- Speaker says deeper coverage will come later in project management classes.

## Interview framing (what to say as a DevOps engineer)

# 
- If asked about SDLC pillars and DevOps focus:
- SDLC includes planning, design, build, test, deploy
- As DevOps: focus on “automating and improving the efficiency of building, testing, and deployment”

## Closing

# 
- SDLC is presented as an industry-wide standard whether on cloud or on-prem.
- Invites questions in comments or via LinkedIn.
- Asks viewers to share the free course with others, like the video, and subscribe.
