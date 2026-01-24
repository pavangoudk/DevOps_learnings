- # 
Day 10 DevOps Course: Git Branching Strategy (Feature, Release, Hotfix)

##
- Course context & why this topic matters

Speaker introduces Day 10 of the complete DevOps course (Day 0 to Day 45).
Suggestion for new viewers: watch Day 0 to Day 9 first because they cover “fundamental concepts of devops.”
Git branching strategy is framed as a common DevOps interview question (seen in “top 50” / “top 20” interview question lists).
Organizational goal driving branching strategy:

Deliver releases on time so customers get “new features on time.”
Release frequency examples: 15 days, 1 month, 2 months, 3 months.

Plan for the session:

Cover branching strategy theoretically and practically.
Practical example chosen: Kubernetes (open source project hosted on GitHub), selected because it has “close to 3300 contributors” and still delivers a new version “every three months.”

What is a branch? (before branching strategy)

Speaker pauses to define branch for viewers who may not know it.
“Branch is nothing but… separation.”
Calculator example:

Existing calculator app has addition/subtraction/multiplication/division.
Want to introduce breaking changes and add version 2 with advanced features like percentage.
Instead of changing the default master/main branch directly:

Create a new branch (examples: V2 branch, feature calculator, feature V2, feature advanced calculator)
Do development and testing on that branch
When confident, merge into the existing code (V1 + V2)
Delete the feature branch after merge

Purpose stated:

Create a branch to ensure “new changes… or breaking changes… [are] not affecting the existing functionality.”

Example scenario: Uber adding bikes

Uber originally supports cabs; many customers already use it.
Uber wants to introduce bikes to gain traction/customers.
Developers aren’t sure whether adding bikes will affect the existing customer-facing app.
Approach described:

Create a new branch for bikes
5–6 developers work on bikes, test it
Merge bikes into the existing Uber application when confident
Delete the bikes branch afterward

A “good branching strategy” (calculator walkthrough)

Uses the calculator repo again to build the branching strategy model.

Main/Master branch (active ongoing changes)

Existing calculator codebase on a date example: “15th Jan.”
Continuous commits happen on this existing repository as customer issues are found and fixed.

Feature branches (for big/new features)

When 2–3 developers decide to add a big new feature:

Uncertain completion time and risk to existing branch
Create a new branch from the current point.

Naming example:

feature XYZ or feature percentage

Workflow described:

Create the feature branch
Develop on it
After testing and confidence, merge feature branch into main/master

Multiple feature branches can exist at the same time:

Example feature branches: feature percentage, feature exponential

Summary statement:

Feature branches exist for “any new feature,” especially “big” features or ones with “breaking changes,” and are merged back into main/master.

Release branches (how code is shipped)

Speaker transitions: after main/master and feature branches, introduces release branches.
Definition and purpose:

A release branch is created when you’re ready to deliver to customers; you “build from release branches” and “ship” from them.

Why not release directly from master:

“Master is usually kept for active development.”
While running end-to-end and functionality testing on the release, you don’t want people to actively push new changes into what you are validating.
Release branch is “cut” at a point: “till now… changes… are very fine and I want to deliver these changes to the customer.”

Repeatable cycle described:

Active work continues on master
Developers create and merge feature branches
When features/fixes are ready for a release, create a release branch
Ship to customer from the release branch

### GitHub (practical demo surface)

Speaker switches from theory to GitHub screen to show Kubernetes’ branching model.

Kubernetes example: master, feature, and release branches in practice

Kubernetes repo is shown as following the same approach:

A master branch where people actively work.
Multiple feature branches (examples mentioned):

feature rate limiting
feature server set applying
feature workload GA

Contributor distribution example:

“10 developers” on one feature, “10” on another, “20” on another; then merge back to master when changes are good (and branches may be deleted).

Release branch naming example:

Current: release 1.26
Future example: create release 1.27 (speaker mentions April as an example release timing).

Once a release branch is created:

Active development continues only on master
Testing happens on the release branch
The release branch contains “the code that will go to your customer.”

Hotfix branches (fourth branch type)

Speaker adds that sometimes there are hotfix branches.
Use case:

“Very critical issue” in production that must be fixed immediately.

Characteristics:

Very quick, short-lived (branch can live “one day two days”).
Make changes, test, and deliver.

Merge rules:

Hotfix changes should be delivered to master and the release branch, then ship release branch code to customer.

Speaker summarizes the four branch types:

master branch, feature branches, release branches, hotfix branches.

Key “tricky” rule emphasized

Hotfix changes should go “both into your master branch as well as your release branches.”
Rationale:

Shipping happens from release branches.
Master/main must stay up to date.

Cascade principle: all changes should flow back to master

Speaker reiterates:

Any branch changes (feature, release, hotfix) “should be merged back to the master.”
Master/main is what people reference for “latest version of your application.”

Release clarification:

“Release always happens from your release branches.”

Uber example revisited (full branching flow)

Speaker re-explains with Uber to reinforce the model:

Uber is a cab app; source code hosted on GitHub.
Create feature bikes, develop for 10–15 days / 1–2 months.
Active cab development continues in parallel.
When confident, merge bikes into master; delete feature branch.
Later introduce feature_intercity, develop and merge back.
After 3–6 months, cut a release branch from latest master:

Example: currently V2, create release V3

Test on release V3 and ship that version to customers.

Interview-style recap questions (as listed by speaker)

Common branch besides feature/release/master:

Bugfix/Hotfix branch
Used when a bug is reported in production shortly after release; must be merged to master and all supported release branches.

From which branch do you perform releases?

Release branch

What is a feature branch?

For introducing “new breaking changes” to existing functionality.

Which branch is always updated/up-to-date?

master / trunk / main

Closing guidance

Speaker suggests exploring branching strategies in other open-source repos:

Docker, Kubernetes, Istio, Jenkins, etc.

Notes this model is commonly followed as a good practice across organizations.
Invites questions via comments/LinkedIn/YouTube messages and asks viewers to share the content with others learning DevOps.

-
