# One Commit Needed From Another Branch – Git Cherry-Pick

## Introduction

One of the most common real-time Git scenarios is:

> A specific fix exists in another branch, but you don't want the entire branch.

Instead of merging the complete branch, you can bring only the required commit using:

```bash
git cherry-pick
```

Think of Cherry-Pick as:

> "I want this commit only, not the entire branch."

---

# What is Git Cherry-Pick?

Cherry-pick allows you to:

* Copy a specific commit
* Apply it to another branch
* Without merging the complete branch

---

# Example

Feature Branch:

```text
A → B → C → D
```

Main Branch:

```text
A → B
```

Suppose commit:

```text
D
```

contains an important bug fix.

Instead of merging the entire feature branch:

```bash
git merge feature-branch
```

You can bring only commit D.

---

# How Cherry-Pick Works

Current Branch:

```text
Main

A → B
```

Feature Branch:

```text
A → B → C → D
```

Run:

```bash
git cherry-pick D
```

Result:

```text
Main

A → B → D'
```

Git creates a new commit with the same changes.

---

# Real-Time Scenario 1

## Urgent Production Fix

Developer fixed:

```text
Login Authentication Bug
```

in:

```text
feature-auth
```

branch.

Production team needs the fix immediately.

But feature branch also contains:

* Unfinished work
* Experimental changes
* New features

We cannot merge the entire branch.

---

### Solution

Find commit:

```bash
git log --oneline
```

Example:

```text
a1234 Fix login authentication
```

Switch:

```bash
git checkout main
```

Cherry-pick:

```bash
git cherry-pick a1234
```

Only login fix comes to main branch.

---

# Real-Time Scenario 2

## Release Branch Needs One Fix

Current Release Branch:

```text
release-v1.2
```

Developer already fixed:

```text
Payment Timeout Issue
```

inside:

```text
develop
```

Need only that fix in release.

Use:

```bash
git cherry-pick commit-id
```

No need to merge entire develop branch.

---

# Real-Time Scenario 3

## Production Hotfix

Developer fixed:

```text
500 Internal Server Error
```

in:

```text
hotfix branch
```

QA environment also requires same fix.

Instead of redoing changes manually:

```bash
git cherry-pick commit-id
```

Apply same fix.

---

# How To Find Commit ID

```bash
git log --oneline
```

Example:

```text
a1234 Login Fix
b2345 Payment Enhancement
c3456 Dashboard Feature
```

Copy:

```text
a1234
```

---

# Cherry-Pick Command

```bash
git cherry-pick a1234
```

---

# Multiple Commits

Cherry-pick multiple commits:

```bash
git cherry-pick a1234 b2345 c3456
```

---

# Cherry-Pick Range

```bash
git cherry-pick A^..D
```

Copies:

```text
A → B → C → D
```

range.

---

# What Happens Internally?

Git does NOT move commit.

Git does NOT merge branch.

Git:

```text
Copies Changes
       ↓
Creates New Commit
       ↓
Applies To Current Branch
```

---

# Cherry-Pick Conflict

Sometimes conflict occurs.

Example:

Current Branch:

```java
String env = "prod";
```

Incoming Commit:

```java
String env = "staging";
```

Git cannot decide.

Conflict appears.

---

# Resolve Conflict

Check:

```bash
git status
```

Resolve file manually.

Stage:

```bash
git add .
```

Continue:

```bash
git cherry-pick --continue
```

---

# Abort Cherry-Pick

If changes are not required:

```bash
git cherry-pick --abort
```

Restores branch to original state.

---

# Common Interview Question

## When Do You Use Cherry-Pick?

I use cherry-pick when:

* One specific fix needed
* Hotfix needed urgently
* Release branch needs selected commit
* Avoid merging unfinished code

---

# Common Interview Question

## Cherry-Pick vs Merge

### Merge

Brings:

```text
Entire Branch
```

---

### Cherry-Pick

Brings:

```text
Specific Commit Only
```

---

# Example

Feature Branch Contains:

```text
Login Fix
Dashboard Feature
Payment Feature
Notification Feature
```

Production only needs:

```text
Login Fix
```

Use:

```bash
git cherry-pick
```

instead of merge.

---

# Common Interview Question

## Cherry-Pick vs Rebase

### Rebase

Moves branch history.

---

### Cherry-Pick

Copies selected commit.

---

# Production Incident Example

During a release, a critical authentication fix was available in the develop branch.

The develop branch also contained multiple unfinished features that were not approved for production.

Instead of merging the entire branch, we identified the required commit using:

```bash
git log --oneline
```

and executed:

```bash
git cherry-pick commit-id
```

The authentication fix was deployed immediately without introducing unfinished features into production.

---

# Commands Summary

```bash
git log --oneline

git cherry-pick <commit-id>

git cherry-pick commit1 commit2

git cherry-pick A^..D

git cherry-pick --continue

git cherry-pick --abort
```

---

# Interview Answer

> "I use git cherry-pick when I need a specific commit from another branch without merging the entire branch. A common example is when a production hotfix exists in a feature or develop branch, but the branch contains unfinished work. In that situation, I identify the required commit using git log and use git cherry-pick to bring only that fix into the target branch."
