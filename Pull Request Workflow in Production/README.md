# Pull Request Workflow in Production: Git & GitHub Explained Simply
 
If you are learning Git and GitHub, you have probably learned commands like:
 
```
git add
git commit
git push
git branch
git merge
```
 
But there is one question that often comes next:
 
![Pull Request Workflow in Production](./images/ChatGPT%20Image%20Aug%2016,%202026,%2006_56_09%20PM.png)

> How do developers actually use Git in a real company?
 
You usually don't just make changes directly on `main` and push them. Instead, companies use a workflow called a **Pull Request workflow**.
 
I recently practiced this workflow using a small GitHub repository, and this is my attempt to explain it as simply as possible.
 
---
 
## First, What Is a Pull Request?
 
Imagine your team is writing a school project. There is one final notebook that everyone trusts. Let's call it `main`.
 
You don't want everyone directly writing in that notebook because someone could accidentally erase something or write something incorrect.
 
So instead, each person gets their own working copy. You make your changes there. When you're finished, you tell your teammates:
 
> "I have finished my work. Can you check it before we put it into the main project?"
 
That request is basically a Pull Request.
 
**A Pull Request is a request to review and merge your changes into another branch, usually `main`.**
 
---
 
## The Production Workflow
 
A typical production workflow looks like this:
 
```
Create branch
      ↓
Make changes
      ↓
Commit changes
      ↓
Push branch to GitHub
      ↓
Create Pull Request
      ↓
Code review + automated checks
      ↓
Fix issues if required
      ↓
Approval
      ↓
Merge into main
      ↓
Delete feature branch
      ↓
CI/CD pipeline
      ↓
Production
```
 
Let's break this down step by step.
 
---
 
### Step 1: Start from a Stable `main` Branch
 
The `main` branch usually represents the stable version of the project.
 
```
main
  |
  A
```
 
Developers generally don't directly make changes to this branch. Instead, they create a separate branch.
 
### Step 2: Create a Working Branch
 
Suppose I'm adding a login feature.
 
```bash
git checkout -b feature/login
```
 
This creates a new branch and switches me to it.
 
```
main
  |
  A
  |
feature/login
```
 
My work is isolated from `main`. I can make mistakes here without immediately affecting the stable branch.
 
**Why use branches?**
 
Because different developers may be working on different things at the same time.
 
```
main
 |
 ├── feature/login
 ├── feature/payment
 ├── feature/search
 └── bugfix/cart
```
 
Everyone can work independently.
 
### Step 3: Make Changes Locally
 
Now I work on my login feature. Maybe I modify:
 
```
login.js
login.css
README.md
```
 
I test everything locally, then check:
 
```bash
git status
```
 
This tells me what has changed, for example:
 
```
modified: login.js
modified: login.css
```
 
### Step 4: Commit My Changes
 
Once I'm happy with the changes:
 
```bash
git add .
git commit -m "feat: add login functionality"
```
 
Now my changes are stored in my local Git history. I can make multiple commits if necessary:
 
```
A → B → C → D
```
 
where:
- `A` = original project
- `B` = login UI
- `C` = login validation
- `D` = bug fix
### Step 5: Push the Branch to GitHub
 
Until now, my branch only exists on my computer. I need to send it to GitHub:
 
```bash
git push -u origin feature/login
```
 
Now GitHub has my branch too.
 
```
My laptop                    GitHub
feature/login  ───────────→  feature/login
```
 
This allows other developers to see and review my work.
 
### Step 6: Create a Pull Request
 
Now I go to GitHub and create a Pull Request:
 
```
feature/login → main
```
 
I'm basically saying: "I've finished my login feature. Please review my changes and merge them into `main` if everything looks good."
 
The Pull Request usually contains:
 
- Title
- Description
- Changed files
- Commit history
- Reviewers
- Automated checks
- Comments
### Step 7: Code Review
 
Now another developer reviews my changes and looks at the diff. A diff basically shows what changed, for example:
 
```diff
+ function loginUser() {
+   authenticateUser();
+ }
```
 
The reviewer might say "This looks good," or they might say "Please handle the error case here." This is called a **review comment**.
 
### Step 8: Fix Review Comments
 
Suppose the reviewer asks me to fix something. I don't create another Pull Request — I simply make the change on the same branch:
 
```bash
git add .
git commit -m "fix: handle login errors"
git push
```
 
The existing Pull Request automatically gets updated.
 
```
PR
 ↓
Reviewer finds problem
 ↓
Developer fixes it
 ↓
Commit
 ↓
Push
 ↓
PR updates automatically
 ↓
Reviewer checks again
```
 
This loop can happen several times.
 
### Step 9: Automated Checks
 
This is where Git and DevOps start connecting. A production repository will often have automated checks that run when a Pull Request is created or updated.
 
```
Pull Request
     |
     ├── Unit tests
     ├── Build
     ├── Code quality check
     ├── Security scan
     ├── Docker build
     └── Infrastructure validation
```
 
You might see:
 
```
✅ Tests passed
✅ Build passed
✅ Security scan passed
❌ Docker build failed
```
 
If something fails, the PR shouldn't normally be merged. I fix the problem, commit again, and push. Then the checks run again, until eventually:
 
```
✅ Tests
✅ Build
✅ Security
✅ Docker
```
 
Now the Pull Request is ready.
 
### Step 10: Approval
 
The reviewer approves the Pull Request:
 
```
Reviewer: Approved ✅
```
 
Depending on the company's rules, you might need **1 approval** or **2 approvals**. Some companies also require all automated checks to pass before the merge button becomes available.
 
### Step 11: Merge Into `main`
 
Now the Pull Request can be merged.
 
```
feature/login
      |
      | merge
      ↓
main
```
 
Your login feature is now part of the official project.
 
### Step 12: Delete the Feature Branch
 
Once the feature is merged, the branch may no longer be needed. You can delete it from GitHub, then locally:
 
```bash
git switch main
git pull origin main
git branch -d feature/login
```
 
Now the repository stays clean instead of having dozens of old feature branches lying around.
 
```
main
 |
 A
 |
 B
 |
 C
```
 
---
 
## What Happens After the Merge?
 
This is where the workflow becomes particularly important for DevOps. In many companies, merging into `main` triggers a CI/CD pipeline.
 
```
Pull Request
      ↓
Code Review
      ↓
Approval
      ↓
Merge into main
      ↓
CI/CD Pipeline
      ↓
Build
      ↓
Test
      ↓
Docker Image
      ↓
Deploy
      ↓
Kubernetes
      ↓
Production
```
 
So a developer doesn't necessarily manually deploy their code. The Git workflow can become the starting point for the entire deployment process.
 
---
 
## A Real DevOps Example
 
Imagine I'm a DevOps engineer working on Kubernetes. I need to increase the CPU limit of an application.
 
```bash
git checkout -b chore/update-k8s-resources
```
 
I modify:
 
```yaml
resources:
  limits:
    cpu: 500m
    memory: 512Mi
```
 
Then:
 
```bash
git add .
git commit -m "chore: update Kubernetes resource limits"
git push -u origin chore/update-k8s-resources
```
 
I create a Pull Request. Now the company pipeline might automatically perform:
 
```
YAML validation
       ↓
Kubernetes manifest validation
       ↓
Security scanning
       ↓
Terraform validation
       ↓
Docker build
       ↓
Automated tests
```
 
If everything passes:
 
```
✅ All checks passed
```
 
A reviewer approves the PR. Then:
 
```
Merge
  ↓
main
  ↓
CI/CD
  ↓
Deployment
```
 
This is how a simple Git Pull Request can eventually lead to a production deployment.
 
---
 
## Why Don't We Just Push Directly to `main`?
 
You technically can, if the repository allows it:
 
```bash
git checkout main
git add .
git commit -m "my changes"
git push origin main
```
 
But imagine a company with 100 developers:
 
- Someone accidentally pushes broken code.
- Someone else pushes unfinished code.
- Someone commits a secret.
- Someone introduces a security vulnerability.
- Someone breaks the production configuration.
That's risky. Instead, companies protect `main`.
 
```
             main
              🔒
              ↑
              |
       Pull Request
              ↑
              |
       feature branch
```
 
The Pull Request acts as a safety gate.
 
### What Does Protected `main` Mean?
 
A company can configure GitHub so developers cannot directly push to `main`. Instead, they must:
 
```
Create branch
      ↓
Pull Request
      ↓
Review
      ↓
Automated checks
      ↓
Approval
      ↓
Merge
```
 
This is called **branch protection**.
 
---
 
## The Whole Workflow in One Picture
 
```
                ┌─────────────────────┐
                │        main         │
                │   Stable / Protected│
                └──────────┬──────────┘
                           │
                           │ branch
                           ↓
                ┌─────────────────────┐
                │   feature/login     │
                └──────────┬──────────┘
                           │
                    Make changes
                           │
                           ↓
                       git commit
                           │
                           ↓
                       git push
                           │
                           ↓
                ┌─────────────────────┐
                │    Pull Request     │
                └──────────┬──────────┘
                           │
                 ┌─────────┴─────────┐
                 ↓                   ↓
           Code Review          CI/CD Checks
                 │                   │
                 └─────────┬─────────┘
                           ↓
                       Approval
                           │
                           ↓
                      Merge to main
                           │
                           ↓
                    CI/CD Pipeline
                           │
                           ↓
                       Production
```
 
---
 
## The Most Important Thing I Learned
 
When I first learned Git, I thought Git was mainly about commands:
 
```
git add
git commit
git push
git pull
```
 
But Git in a real engineering team is much more than that. It's about how a team safely changes software.
 
The important workflow is:
 
```
Branch
 ↓
Change
 ↓
Commit
 ↓
Push
 ↓
Pull Request
 ↓
Review
 ↓
Automated validation
 ↓
Approval
 ↓
Merge
 ↓
Deployment
```
 
The commands are just the tools that make this workflow possible.
 
---
 
## Git vs GitHub
 
One thing that helped me understand this better was separating Git from GitHub.
 
### Git
 
Git is the version control system running locally. It handles things like:
 
```
git init
git add
git commit
git branch
git merge
git reset
git revert
```
 
### GitHub
 
GitHub is a platform built around Git. It provides things like:
 
- Remote repositories
- Pull Requests
- Code Reviews
- Branch Protection
- GitHub Actions
- Issues
- CI/CD
So when I create a Pull Request, I'm using GitHub's collaboration features, not a special `git pull-request` command.
 
---
 
## My Simple Mental Model
 
If you're just starting with Git, remember this:
 
| Term | Meaning |
|---|---|
| **Branch** | My workspace |
| **Commit** | Save my work |
| **Push** | Send my work to GitHub |
| **Pull Request** | Ask someone to review my work |
| **Review** | Check my work |
| **CI checks** | Let the machines check my work |
| **Merge** | Put my work into the official project |
| **CI/CD** | Take the approved code toward production |
 
Once you understand this, the entire Pull Request workflow becomes much less confusing.
 
