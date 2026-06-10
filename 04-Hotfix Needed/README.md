# Production Hotfix Needed – Git Stash, Stash Pop & Branch Switching 

## Introduction

One of the most common real-time Git scenarios is:

> You are working on a feature branch.
>
> Your work is incomplete.
>
> Suddenly a production issue is reported.
>
> You need to switch branches immediately and fix production.

This is where:

```bash
git stash
git stash pop
```

become extremely useful.

In real DevOps and development environments, stash is used almost daily during urgent production fixes.

---

# Real-Time Scenario

Imagine you are working on:

```text
feature/user-dashboard
```

You have modified:

```text
dashboard.js
dashboard.css
api.js
```

But changes are not complete.

Suddenly:

```text
Production Issue Reported
```

Users cannot login.

Manager says:

```text
"Please fix production immediately."
```

---

# Problem

You cannot switch branches because Git detects uncommitted changes.

Current state:

```bash
git status
```

Output:

```text
modified: dashboard.js
modified: api.js
modified: dashboard.css
```

---

# Option 1 (Bad Practice)

Commit incomplete code.

```bash
git add .
git commit -m "Work in progress"
```

Problems:

* Dirty commit history
* Half-completed feature pushed
* Confuses reviewers

---

# Better Solution

Use:

```bash
git stash
```

---

# What is Git Stash?

Git stash temporarily stores your uncommitted changes in a hidden Git storage area.

Think of it as:

```text
Temporary Locker
```

Git stores:

* Modified files
* Staged files
* Untracked files (optional)

and restores your working directory to a clean state.

---

# How Stash Works Internally

Before stash:

```text
Feature Branch
        ↓
Uncommitted Changes
```

Run:

```bash
git stash
```

After stash:

```text
Changes Stored Safely
        ↓
Working Directory Clean
```

Now Git allows branch switching.

---

# Command

```bash
git stash
```

Output:

```text
Saved working directory and index state
```

---

# Real-Time Flow

```text
Feature Work
      ↓
Production Issue
      ↓
git stash
      ↓
Switch Branch
      ↓
Fix Production
      ↓
Return To Feature Branch
      ↓
git stash pop
      ↓
Continue Work
```

---

# Branch Switching During Production Issue

After stash:

Switch to production branch.

```bash
git checkout main
```

or

```bash
git checkout production
```

---

# Why?

Now working directory is clean.

Git allows switching safely.

---

# Real-Time Example

Current branch:

```text
feature-payment-ui
```

Production issue:

```text
Login Service Down
```

Store changes:

```bash
git stash
```

Switch:

```bash
git checkout main
```

Create hotfix:

```bash
git checkout -b hotfix/login-fix
```

---

# Why Create Hotfix Branch?

Never fix directly on main.

Industry standard:

```text
main
   ↓
hotfix/login-fix
```

---

# Production Hotfix Flow

```text
Main Branch
      ↓
Create Hotfix Branch
      ↓
Fix Bug
      ↓
Test
      ↓
Commit
      ↓
Merge To Main
      ↓
Deploy
```

---

# Example

```bash
git checkout main

git pull origin main

git checkout -b hotfix/login-fix
```

---

# Fix Issue

Make changes.

Commit:

```bash
git add .

git commit -m "Fix login authentication issue"
```

Push:

```bash
git push origin hotfix/login-fix
```

Create PR.

Deploy.

Production restored.

---

# Returning To Feature Work

After production issue resolved:

Switch back:

```bash
git checkout feature-payment-ui
```

---

# Check Stashed Changes

```bash
git stash list
```

Output:

```text
stash@{0}: WIP on feature-payment-ui
```

---

# Why Important?

Multiple stashes may exist.

Need to identify correct stash.

---

# View Stash Content

```bash
git stash show
```

---

# Detailed View

```bash
git stash show -p
```

Shows exact code changes stored.

---

# Restore Stashed Work

Use:

```bash
git stash pop
```

---

# What Happens?

Git:

1. Restores changes
2. Removes stash entry

---

# Real-Time Flow

```text
Stashed Changes
       ↓
git stash pop
       ↓
Changes Restored
       ↓
Stash Deleted
```

---

# Example

Before:

```text
Working Directory Clean
```

Run:

```bash
git stash pop
```

After:

```text
dashboard.js restored
dashboard.css restored
api.js restored
```

Continue development.

---

# Important Difference

## git stash apply

```bash
git stash apply
```

Restores changes.

Keeps stash.

---

## git stash pop

```bash
git stash pop
```

Restores changes.

Deletes stash.

---

# Interview Question

## When Do You Use Apply?

When:

* Unsure if changes will work
* Want backup copy

---

## When Do You Use Pop?

When:

* Ready to continue work
* No longer need backup

---

# Real-Time Scenario 2

Developer working on:

```text
Payment Module
```

Production issue:

```text
502 Bad Gateway
```

Appears.

Developer:

```bash
git stash
```

Switches:

```bash
git checkout main
```

Creates:

```bash
git checkout -b hotfix-nginx
```

Fixes Nginx configuration.

Deploys.

Returns:

```bash
git checkout feature-payment
git stash pop
```

Continues feature work.

---

# Stashing Untracked Files

By default stash ignores:

```text
new files
```

Example:

```text
new.js
```

---

# Include Untracked Files

```bash
git stash -u
```

or

```bash
git stash --include-untracked
```

---

# Real-Time Scenario

Created:

```text
new-payment-api.js
```

Not yet committed.

Need production fix.

Use:

```bash
git stash -u
```

to preserve file.

---

# Named Stashes

Useful in real projects.

```bash
git stash save "payment feature work"
```

---

# Check List

```bash
git stash list
```

Output:

```text
stash@{0}: payment feature work
stash@{1}: dashboard changes
```

---

# Delete Stash

Delete specific stash:

```bash
git stash drop stash@{0}
```

---

# Delete All Stashes

```bash
git stash clear
```

---

# Common Interview Questions

## Why Not Commit Incomplete Code?

Because:

* Pollutes history
* Creates unnecessary commits
* Makes code reviews difficult

Stash is cleaner.

---

## What Happens If I Forget Stash?

Git prevents branch switching when files conflict.

Example:

```text
Your local changes would be overwritten
```

---

## Can Stash Be Recovered?

Yes.

Git stores stash internally.

Check:

```bash
git stash list
```

---

## Is Stash Branch Specific?

No.

Stashes belong to repository.

Any branch can access them.

---

# Production Incident Example

A developer was working on a dashboard feature.

Before completion:

```text
Critical Production Login Issue
```

reported.

Instead of committing incomplete work:

```bash
git stash
```

was used.

Developer switched to:

```bash
git checkout main
```

created:

```bash
git checkout -b hotfix-login
```

fixed issue, deployed, merged.

After production stabilization:

```bash
git checkout feature-dashboard

git stash pop
```

Feature work restored exactly where it was left.

No work lost.

No unnecessary commits created.

---

# Commands Summary

```bash
git stash

git stash -u

git stash list

git stash show

git stash show -p

git stash apply

git stash pop

git stash drop stash@{0}

git stash clear
```

---

# Final Interview Answer

> "In real projects, I use git stash whenever I have incomplete work and need to switch context quickly, especially during production incidents. I stash my current changes, switch to a hotfix branch, resolve the production issue, deploy the fix, and then return to my feature branch using git stash pop to continue exactly where I left off. This helps maintain clean commit history while ensuring no work is lost."
