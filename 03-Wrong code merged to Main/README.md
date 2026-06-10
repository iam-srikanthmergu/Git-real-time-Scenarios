# Git Revert – Complete Real-Time Guide

## Introduction

One of the most important Git concepts in production environments is:

```bash
git revert
```

Many developers use:

```bash
git reset --hard
```

to undo changes.

But in real projects, especially on:

* Main branch
* Production branch
* Shared branches

we prefer:

```bash
git revert
```

because it preserves history.

---

# What is Git Revert?

Git Revert is used to:

> Undo a commit by creating a new commit.

Unlike reset:

* It does NOT delete history
* It does NOT rewrite history
* It is safe for shared repositories

---

# Understanding With Example

Current History:

```text
A → B → C → D
            ↑
          HEAD
```

Suppose commit D introduced a bug.

Instead of deleting D:

```bash
git revert D
```

Git creates:

```text
A → B → C → D → E
                ↑
              HEAD
```

Where:

```text
E = Revert Commit
```

Commit D still exists.

Git simply creates another commit that reverses its changes.

---

# Why Revert Is Preferred In Production

Imagine:

```text
Developer A
Developer B
Developer C
```

all working on:

```text
main
```

If Developer A executes:

```bash
git reset --hard HEAD~1
git push --force
```

everyone's history changes.

This creates:

* Merge issues
* Pull issues
* Lost commits

---

# Safer Alternative

```bash
git revert HEAD
```

History remains intact.

Everyone can continue working normally.

---

# Basic Command

Undo latest commit:

```bash
git revert HEAD
```

---

# Real-Time Scenario 1

## Wrong Feature Released

Developer deployed:

```text
New Login Feature
```

Production users unable to login.

Need immediate rollback.

Use:

```bash
git revert HEAD
```

Git creates:

```text
Revert "Added Login Feature"
```

Deploy again.

Issue resolved.

---

# Why Not Reset?

Because:

```text
Production Branch
Shared Branch
```

History should never be rewritten.

---

# Real-Time Scenario 2

## Bad Deployment

Commit History:

```text
A → B → C → D
```

D caused:

```text
500 Internal Server Error
```

Revert:

```bash
git revert D
```

Result:

```text
A → B → C → D → Revert-D
```

Application returns to stable state.

---

# Real-Time Scenario 3

## Hotfix Rollback

Developer deployed:

```text
Payment Gateway Changes
```

Customers unable to make payments.

Immediate action:

```bash
git revert HEAD
```

Pipeline triggered.

Production restored.

Root cause investigated later.

---

# What Happens Internally?

Git does NOT remove commit.

Instead:

```text
Bad Commit
        ↓
Git Creates Opposite Commit
```

Example:

Original:

```python
print("Version 2")
```

Revert creates:

```python
print("Version 1")
```

as a new commit.

---

# Revert Specific Commit

Find commit:

```bash
git log --oneline
```

Example:

```text
a1234 Login Feature
b2345 Payment Gateway
c3456 Initial Commit
```

Revert:

```bash
git revert a1234
```

---

# Revert Multiple Commits

```bash
git revert HEAD~3..HEAD
```

Useful during production rollback.

---

# Reverting Merge Commit

Suppose:

```text
Feature Branch
      ↓
Merged Into Main
```

Merge caused issue.

Use:

```bash
git revert -m 1 <merge_commit_id>
```

---

# Real-Time Production Example

Feature branch merged successfully.

After deployment:

```text
Application Crash
```

Investigation showed feature branch introduced bug.

Instead of rewriting history:

```bash
git revert -m 1 merge_commit_id
```

Rollback completed safely.

---

# Reset vs Revert

| Feature                  | Git Reset | Git Revert |
| ------------------------ | --------- | ---------- |
| Rewrites History         | Yes       | No         |
| Removes Commits          | Yes       | No         |
| Creates New Commit       | No        | Yes        |
| Safe For Shared Branches | No        | Yes        |
| Production Usage         | Rarely    | Frequently |

---

# Interview Question

## When Do You Use Reset?

I use reset for:

* Local commits
* Personal branches
* Cleaning history before push

Example:

```bash
git reset --soft HEAD~1
```

---

## When Do You Use Revert?

I use revert for:

* Production branches
* Main branch
* Shared repositories
* Rollbacks

Example:

```bash
git revert HEAD
```

---

# Most Common Interview Question

## A Wrong Commit Is Already Pushed To Main Branch. What Will You Do?

Answer:

"I will use git revert instead of git reset because reset rewrites shared history. Revert creates a new commit that safely undoes the changes while preserving audit history."

---

# Real Production Incident

A developer merged payment service changes into the main branch.

After deployment:

```text
Payment transactions started failing.
```

Since the commit was already pushed and used by multiple developers:

```bash
git revert HEAD
```

was executed.

Pipeline deployed the revert commit.

Application returned to stable state within minutes.

No history was lost.

---

# Final Interview Answer

> "I use git revert whenever I need to undo changes on shared branches or production branches. Unlike git reset, revert preserves history by creating a new commit that reverses previous changes. This makes it the safest rollback mechanism in collaborative environments."
