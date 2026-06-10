# Merge Conflict During Release – Git Merge, Conflict Markers & Resolution 

## Introduction

One of the most common Git problems in real projects is:

> Merge Conflict

Every DevOps Engineer, Developer, and Release Engineer will face merge conflicts sooner or later.

Interviewers often ask:

* What is a merge conflict?
* Why does it happen?
* How do you resolve it?
* Tell me a real production scenario.

Most candidates know the definition but struggle to explain a real-world example.

---

# What is a Merge Conflict?

A merge conflict occurs when Git cannot automatically decide which changes should be kept.

Git sees:

```text
Branch A modified line 10
Branch B modified line 10
```

Git becomes confused.

It doesn't know:

```text
Keep Branch A?
Keep Branch B?
Keep Both?
```

So Git stops the merge and asks for manual intervention.

---

# Real-Time Scenario

Imagine:

Developer A working on:

```text
feature-login
```

Developer B working on:

```text
feature-security
```

Both modify:

```text
application.properties
```

---

# Original File

```properties
server.port=8080
```

---

# Developer A Changes

```properties
server.port=8081
```

---

# Developer B Changes

```properties
server.port=9090
```

---

# Merge Happens

Developer A merges first.

Then Developer B attempts:

```bash
git merge feature-security
```

Git sees:

```text
Same line modified differently
```

Result:

```text
Merge Conflict
```

---

# What Happens Internally?

Git attempts:

```text
Common Ancestor
        ↓
Compare Branch A
        ↓
Compare Branch B
        ↓
Auto Merge
```

If changes occur in different locations:

```text
Merge Successful
```

If changes occur on same lines:

```text
Conflict
```

---

# Real-Time Release Scenario

This is very common during release weekends.

Example:

```text
Release Branch
      ↓
Feature A Merged
      ↓
Feature B Merged
      ↓
Feature C Merged
```

Suddenly:

```text
Merge Conflict
```

appears in deployment configuration.

Release gets delayed.

---

# How To Identify Merge Conflict

Command:

```bash
git merge feature-branch
```

Output:

```text
Auto-merging application.properties
CONFLICT (content): Merge conflict in application.properties
Automatic merge failed
```

---

# Check Status

```bash
git status
```

Output:

```text
both modified: application.properties
```

---

# Why Use git status?

Shows:

* Which files are conflicted
* Which files need resolution

---

# Conflict Markers

Git inserts conflict markers.

Example:

```text
<<<<<<< HEAD

server.port=8081

=======

server.port=9090

>>>>>>> feature-security
```

---

# Understanding Conflict Markers

## HEAD

Current branch version.

```text
<<<<<<< HEAD
```

---

## Separator

```text
=======
```

Divides both versions.

---

## Incoming Branch

```text
>>>>>>> feature-security
```

Incoming branch version.

---

# How To Read It

```text
<<<<<<< HEAD
Current Branch Code
=======
Incoming Branch Code
>>>>>>> feature-branch
```

---

# Real Example

```java
<<<<<<< HEAD

String env = "prod";

=======

String env = "staging";

>>>>>>> feature-config
```

Git asks:

```text
Which version should remain?
```

---

# Manual Resolution

Developer decides.

Final code:

```java
String env = "prod";
```

or

```java
String env = "staging";
```

or

```java
String env = System.getenv("ENV");
```

---

# After Resolution

Stage file:

```bash
git add application.properties
```

---

# Complete Merge

```bash
git commit
```

Git creates merge commit.

---

# Full Resolution Flow

```text
Merge Attempt
      ↓
Conflict Found
      ↓
Open File
      ↓
Review Conflict Markers
      ↓
Choose Correct Code
      ↓
Remove Markers
      ↓
git add
      ↓
git commit
      ↓
Merge Complete
```

---

# Real-Time Scenario 1

## Production Configuration Conflict

Developer A modified:

```yaml
replicas: 2
```

Developer B modified:

```yaml
replicas: 5
```

During release:

```text
deployment.yaml conflict
```

appeared.

Discussion with team:

Production traffic expected to increase.

Final decision:

```yaml
replicas: 5
```

Conflict resolved.

---

# Real-Time Scenario 2

## Kubernetes Deployment Conflict

Developer A changed:

```yaml
image: app:v1
```

Developer B changed:

```yaml
image: app:v2
```

Merge conflict occurred.

Team verified latest tested image.

Resolved:

```yaml
image: app:v2
```

Release continued.

---

# Real-Time Scenario 3

## Jenkins Pipeline Conflict

Developer A:

```groovy
stage('Build')
```

Developer B:

```groovy
stage('Security Scan')
```

Both modified same section.

Merge conflict occurred.

Final Jenkinsfile:

```groovy
stage('Build')

stage('Security Scan')
```

Both changes preserved.

---

# Using Merge Tools

Instead of manually editing:

```bash
git mergetool
```

---

# Why Use Mergetool?

Provides visual comparison.

Useful for:

* Large files
* Complex conflicts

---

# VS Code Resolution

Open:

```bash
code .
```

VS Code shows:

```text
Accept Current Change
Accept Incoming Change
Accept Both Changes
Compare Changes
```

---

# Why VS Code Useful?

Reduces manual mistakes.

---

# Release Engineer Approach

During production releases:

I follow:

```text
Understand Both Changes
        ↓
Talk To Developers
        ↓
Understand Business Requirement
        ↓
Resolve Conflict
        ↓
Run Validation
        ↓
Merge
```

Never blindly accept changes.

---

# Important Interview Question

## How Do You Prevent Merge Conflicts?

### Frequent Pulls

```bash
git pull origin main
```

Regularly sync branches.

---

### Small Pull Requests

Avoid huge PRs.

---

### Feature Branch Strategy

```text
main
   ↓
feature/*
```

Keeps work isolated.

---

### Communication

Developers discuss shared files.

---

# Important Interview Question

## What Happens If You Resolve Conflict Incorrectly?

Possible outcomes:

```text
Application Failure
Deployment Failure
Production Outage
Configuration Errors
```

Always test after conflict resolution.

---

# Real Production Incident

During a release, two teams modified:

```text
application.yaml
```

One team updated:

```yaml
database_url
```

Another updated:

```yaml
redis_endpoint
```

Merge conflict occurred.

A developer accidentally removed Redis configuration while resolving.

Result:

```text
Application Failed To Start
```

Root Cause:

Incorrect conflict resolution.

Fix:

Reviewed conflict carefully.

Restored missing configuration.

Deployment succeeded.

---

# Commands Summary

```bash
git merge feature-branch

git status

git add file-name

git commit

git mergetool
```

---

# Interview Question

## Difference Between Merge Conflict and Rebase Conflict?

### Merge Conflict

Occurs during:

```bash
git merge
```

Git combines histories.

---

### Rebase Conflict

Occurs during:

```bash
git rebase
```

Git rewrites commit history.

---

# Final Interview Answer

> "A merge conflict occurs when Git cannot automatically determine which changes should be kept because multiple branches modified the same lines of code. During conflict resolution, I first identify the affected files using git status, review the conflict markers, understand both changes, discuss with developers if required, manually resolve the conflict, test the application, and then complete the merge. In production releases, careful conflict resolution is critical because incorrect resolutions can lead to deployment failures or outages."
