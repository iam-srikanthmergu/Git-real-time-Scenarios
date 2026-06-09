# Git Log, Git Reset & Git Reflog – Complete Real-Time Guide

## Introduction

One of the most common Git interview questions is:

> What is the difference between `git log`, `git reset`, and `git reflog`?

Most developers know the commands, but very few understand:

* Why we use them
* When we use them
* What happens internally
* How to recover from mistakes
* Real production scenarios

This guide explains all three commands with practical examples and interview-ready explanations.

---

# Understanding the Commit Flow

Let's assume our Git history looks like this:

```text
A → B → C → D → E
                ↑
              HEAD
```

Current branch is pointing to commit **E**.

---

# 1. Git Log

## What is Git Log?

`git log` is used to view the commit history of a repository.

Think of it as:

> Git's audit trail.

It helps us understand:

* Who made the change
* When it was made
* What was changed
* Which commit introduced the change

---

## Basic Command

```bash
git log
```

Example Output:

```text
commit 8d4f6e2
Author: Srikanth
Date: Jun 10

Added Login Feature
```

---

## Real-Time Use Cases

### Production Issue Investigation

Users report:

```text
Login functionality stopped working
```

First thing we check:

```bash
git log
```

Questions we try to answer:

* Who modified login code?
* Which commit introduced the issue?
* When was the change deployed?

---

## Useful Variations

### Compact History

```bash
git log --oneline
```

Output:

```text
a1234 Added Jenkins Pipeline
b2345 Fixed Login Bug
c3456 Initial Commit
```

Most commonly used in real projects.

---

### Visual Branch History

```bash
git log --oneline --graph --all
```

Output:

```text
* a1234 Feature Branch
|\
| * b2345 Login Changes
|/
* c3456 Main Branch
```

Useful for:

* Merge troubleshooting
* Branch analysis
* Release reviews

---

### View Last 5 Commits

```bash
git log -5
```

---

### View Specific Developer Commits

```bash
git log --author="Srikanth"
```

---

## Interview Answer

> "I use git log to analyze commit history, identify code changes, investigate production issues, and track deployment versions."

---

# 2. Git Reset

## What is Git Reset?

`git reset` moves HEAD to a previous commit.

Think of it as:

> Time travel for your branch.

---

## Example

Current History:

```text
A → B → C → D → E
                ↑
              HEAD
```

Run:

```bash
git reset --hard C
```

Result:

```text
A → B → C
        ↑
      HEAD
```

Commits D and E disappear from branch history.

---

# Types of Git Reset

---

## 1. Soft Reset

### Command

```bash
git reset --soft HEAD~1
```

### What Happens?

Removes:

* Commit

Keeps:

* Staged Changes
* Working Directory Changes

---

### Real-Time Scenario

Developer commits with wrong commit message.

```bash
git commit -m "temp"
```

Need to change message.

Use:

```bash
git reset --soft HEAD~1
```

Recreate commit:

```bash
git commit -m "Proper Commit Message"
```

---

### Interview Answer

> "Soft reset removes commits while preserving staged changes."

---

## 2. Mixed Reset (Default)

### Command

```bash
git reset HEAD~1
```

or

```bash
git reset --mixed HEAD~1
```

---

### What Happens?

Removes:

* Commit

Keeps:

* File Changes

Removes:

* Staging Area

---

### Real-Time Scenario

Developer accidentally staged multiple files.

Needs to unstage them.

```bash
git reset
```

Files remain intact.

---

### Interview Answer

> "Mixed reset removes commits and unstages changes while preserving files."

---

## 3. Hard Reset

### Command

```bash
git reset --hard HEAD~1
```

---

### What Happens?

Deletes:

* Commit
* Staging Area
* Local Changes

Everything is removed.

---

### Real-Time Scenario

Developer added temporary debugging code.

Doesn't want to keep it.

```bash
git reset --hard
```

Removes all local modifications.

---

### Important Warning

Never use hard reset carelessly.

You may lose important work.

---

### Interview Answer

> "Hard reset completely discards commits and local modifications. I use it carefully and never directly on shared branches."

---

# Why Reset Is Dangerous On Shared Branches

Imagine:

```text
Developer A
Developer B
Developer C
```

All are working on:

```text
main
```

You execute:

```bash
git reset --hard
git push --force
```

Now everyone's commit history changes.

This can break other developers' work.

---

## Best Practice

### Use Reset For

* Local branches
* Personal cleanup
* Before code is shared

### Avoid Reset For

* Main branch
* Production branch
* Shared repositories

---

# 3. Git Reflog

## What is Git Reflog?

Git Reflog is Git's recovery mechanism.

Think of it as:

> Git's black box recorder.

Even if commits disappear, Git still remembers where HEAD was previously.

---

## Command

```bash
git reflog
```

Example:

```text
abc123 HEAD@{0}: reset
def456 HEAD@{1}: commit
ghi789 HEAD@{2}: checkout
```

---

## What Does Reflog Track?

Tracks:

* Commits
* Checkouts
* Resets
* Rebases
* Branch Changes

Basically every movement of HEAD.

---

# Real-Time Recovery Scenarios

## Scenario 1 – Accidental Hard Reset

Developer runs:

```bash
git reset --hard HEAD~5
```

Five commits disappear.

---

### Recovery

Check reflog:

```bash
git reflog
```

Find previous commit:

```text
a1b2c3 HEAD@{1}
```

Recover:

```bash
git reset --hard a1b2c3
```

All commits restored.

---

## Scenario 2 – Deleted Branch Recovery

Developer accidentally deletes branch:

```bash
git branch -D feature-login
```

Branch disappears.

---

### Recovery

Find commit:

```bash
git reflog
```

Recreate branch:

```bash
git checkout -b feature-login <commit-id>
```

Branch restored.

---

## Scenario 3 – Bad Rebase

Developer performs:

```bash
git rebase main
```

History becomes messy.

---

### Recovery

Check previous state:

```bash
git reflog
```

Reset:

```bash
git reset --hard <old-commit>
```

Everything restored.

---

## Interview Answer

> "Git reflog is my primary recovery tool whenever commits, branches, or history appear lost."

---

# Understanding What Happens Internally

Many developers think:

```text
git reset = delete
```

Actually:

```text
git reset = move HEAD
```

Git objects still exist.

Reflog remembers where HEAD previously pointed.

That's why recovery is possible.

---

# Real Production Incident

A developer accidentally executed:

```bash
git reset --hard HEAD~10
```

Ten commits disappeared.

Using:

```bash
git reflog
```

We found the previous commit SHA and restored all lost commits within minutes.

Without reflog, several days of development effort could have been lost.

---

# Comparison Table

| Command    | Purpose         | Safe For Shared Branches |
| ---------- | --------------- | ------------------------ |
| git log    | View history    | Yes                      |
| git reset  | Rewrite history | No                       |
| git reflog | Recover history | Yes                      |

---

# When Should You Use Each?

## Use Git Log When

* Investigating changes
* Reviewing commits
* Tracking deployments
* Auditing repository history

---

## Use Git Reset When

* Cleaning local commits
* Removing unwanted changes
* Reorganizing local history

---

## Use Git Reflog When

* Recovering deleted commits
* Recovering deleted branches
* Recovering after reset
* Recovering after bad rebase

---

# Most Common Interview Question

## Difference Between Reset and Revert

### Git Reset

* Rewrites history
* Removes commits
* Best for local branches

### Git Revert

* Creates new commit
* Preserves history
* Best for shared branches

---

# Interview Summary

### Git Log

Used for viewing and auditing commit history.

### Git Reset

Used for modifying local commit history and undoing changes.

### Git Reflog

Used for recovering commits, branches, and history after accidental deletion or reset.

---

# Final Interview Answer

> "In my day-to-day work, I use git log to investigate commit history, git reset to clean up local commits before sharing code, and git reflog as a recovery mechanism whenever commits or branches are accidentally lost. For shared branches, I avoid rewriting history and prefer git revert because it preserves commit history and is safer for team collaboration."
