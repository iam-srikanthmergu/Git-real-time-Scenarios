# Who Changed This File In Production?

## Git Blame, Git Log & File History

## Introduction

One of the most common production support situations is:

> Application was working yesterday.
>
> Today it is failing.
>
> We need to identify:
>
> * Who changed the file?
> * When was it changed?
> * What exactly changed?
> * Why was it changed?

This is where:

```bash
git blame
git log
git show
```

become extremely useful.

These commands help us perform:

* Root Cause Analysis (RCA)
* Production Troubleshooting
* Audit Tracking
* Change Investigation

---

# Real-Time Scenario

Suppose production application suddenly starts failing.

Error:

```text
Database Connection Failed
```

Investigation shows issue exists in:

```text
application.properties
```

Question:

```text
Who modified this file?
When was it modified?
What exactly was changed?
```

---

# Approach

My investigation usually follows:

```text
Production Issue
        ↓
Identify File
        ↓
Check File History
        ↓
Identify Commit
        ↓
Identify Developer
        ↓
Review Changes
        ↓
Root Cause Analysis
```

---

# 1. Git Blame

## What is Git Blame?

Git Blame shows:

```text
Who modified each line
When it was modified
Which commit modified it
```

Think of it as:

> Line-by-line ownership tracking.

---

# Command

```bash
git blame application.properties
```

---

# Example Output

```text
a1234 (Srikanth) server.port=8080

b4567 (Rahul) database.url=mysql

c7890 (John) redis.host=redis
```

---

# Understanding Output

Git shows:

```text
Commit ID
Developer
Line Content
```

---

# Why Use Git Blame?

When production issue occurs:

We need to identify:

```text
Which developer changed this line?
```

without reviewing hundreds of commits.

---

# Real-Time Scenario 1

Production issue:

```text
Database Connection Failure
```

Configuration file:

```text
application.properties
```

contains:

```properties
database.url=wrong-server
```

Run:

```bash
git blame application.properties
```

Output:

```text
d1234 Rahul database.url=wrong-server
```

Now we know:

* Developer = Rahul
* Commit = d1234

Next step:

Investigate commit.

---

# Real-Time Scenario 2

Kubernetes Deployment Failure

Deployment YAML:

```yaml
replicas: 0
```

Application unavailable.

Question:

```text
Who changed replicas from 3 to 0?
```

Use:

```bash
git blame deployment.yaml
```

Find:

* Developer
* Commit
* Timestamp

---

# Interview Answer

> "I use git blame when I need to identify who modified a specific line of code or configuration. It is extremely useful during production incidents and root cause analysis."

---

# Limitation of Git Blame

Git Blame shows:

```text
Who changed line
```

but not:

```text
Why it changed
```

For that we use:

```bash
git log
```

---

# 2. Git Log

## What is Git Log?

Git Log shows:

```text
Commit History
```

including:

* Commit ID
* Author
* Date
* Commit Message

---

# Command

```bash
git log
```

---

# Real-Time Usage

Suppose:

```text
application.properties
```

caused outage.

Check history:

```bash
git log application.properties
```

---

# Why?

Shows only commits related to that file.

---

# Example Output

```text
a1234 Updated Database URL

b2345 Added Redis Config

c3456 Initial Setup
```

---

# Benefits

Quickly identify:

* Recent changes
* Risky deployments
* Configuration updates

---

# Most Common Production Usage

After deployment:

```text
Application Started Failing
```

Check:

```bash
git log --oneline
```

Review:

```text
Last 5 Commits
```

Often issue appears in recent changes.

---

# Useful Git Log Commands

## Compact View

```bash
git log --oneline
```

---

## File Specific History

```bash
git log application.properties
```

---

## Last Five Commits

```bash
git log -5
```

---

## Specific Developer

```bash
git log --author="Srikanth"
```

---

# Interview Answer

> "I use git log to investigate commit history, understand recent changes, and identify commits related to production incidents."

---

# 3. File History Investigation

Often we need more than:

```text
Who changed it?
```

We need:

```text
What exactly changed?
```

---

# Command

```bash
git log -p application.properties
```

---

# Why?

Shows:

```text
Commit History
+
Actual Code Changes
```

---

# Example

```text
- database.url=prod-db

+ database.url=test-db
```

Now root cause becomes obvious.

---

# Real-Time Scenario

Production outage.

Application trying to connect to:

```text
test-db
```

instead of:

```text
prod-db
```

Using:

```bash
git log -p application.properties
```

identified exact change.

---

# Git Show

Another useful command:

```bash
git show commit-id
```

---

# Example

```bash
git show a1234
```

Shows:

```text
Files Changed
Lines Added
Lines Removed
```

---

# Why Use It?

When Git Blame identifies commit:

```text
a1234
```

Next step:

```bash
git show a1234
```

Review full modification.

---

# Real-Time Production Investigation Flow

```text
Application Failure
        ↓
Identify Impacted File
        ↓
git blame
        ↓
Find Developer & Commit
        ↓
git show commit-id
        ↓
Review Change
        ↓
Root Cause Analysis
        ↓
Fix
```

---

# Real-Time Scenario 3

## Jenkins Pipeline Failure

Pipeline suddenly failing.

Issue found in:

```text
Jenkinsfile
```

Question:

```text
Who modified deployment stage?
```

Run:

```bash
git blame Jenkinsfile
```

Find commit.

Review:

```bash
git show commit-id
```

Found deployment script modification.

Issue resolved.

---

# Real-Time Scenario 4

## Kubernetes Outage

Pods not starting.

Investigation:

```text
deployment.yaml
```

changed recently.

Check:

```bash
git blame deployment.yaml
```

Identify:

```text
Image Tag Changed
```

Review commit:

```bash
git show commit-id
```

Found invalid image version.

---

# Common Interview Questions

## Difference Between Git Blame And Git Log

### Git Blame

Shows:

```text
Who changed each line
```

---

### Git Log

Shows:

```text
Commit History
```

---

# Example

```bash
git blame app.py
```

Line-level investigation.

---

```bash
git log app.py
```

File-level history.

---

# Common Interview Question

## How Do You Find Who Introduced A Production Bug?

Answer:

1. Identify affected file.
2. Run:

```bash
git blame filename
```

3. Identify commit.
4. Review commit:

```bash
git show commit-id
```

5. Analyze reason for change.
6. Decide rollback or fix.

---

# Common Interview Question

## How Do You Check Changes Made To A File Over Time?

Use:

```bash
git log -p filename
```

Shows:

* History
* Diffs
* Evolution of file

---

# Real Production Incident

Production application failed to connect to database after deployment.

Investigation:

```bash
git blame application.properties
```

identified recent commit.

Review:

```bash
git show commit-id
```

revealed database endpoint changed accidentally from:

```text
prod-db
```

to

```text
test-db
```

Fix:

Reverted configuration.

Application recovered immediately.

---

# Commands Summary

```bash
git blame filename

git log

git log --oneline

git log filename

git log -p filename

git show commit-id

git log --author="name"
```

---

# Interview Answer

> "When investigating production issues, I typically use git blame to identify who modified a specific line, git log to review file history and recent commits, and git show or git log -p to understand exactly what changes were introduced. This helps perform root cause analysis quickly and determine whether a rollback, revert, or code fix is required."
