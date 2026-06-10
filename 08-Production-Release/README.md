# Production Release Strategy

## Tags, Release Branches, Hotfix Branches & Branching Strategy

## Introduction

One of the most important Git concepts in real projects is managing releases safely.

Interviewers often ask:

* How do you release code to production?
* What are Git Tags?
* Why use Release Branches?
* What are Hotfix Branches?
* Explain your Branching Strategy.
* How do you rollback a production release?

A good release strategy helps teams:

* Release safely
* Rollback quickly
* Support multiple environments
* Fix production issues without affecting ongoing development

---

# Why Do We Need A Release Strategy?

Imagine:

```text
20 Developers
50 Features
Multiple Environments
Production Deployments
```

Without a strategy:

* Releases become chaotic
* Rollbacks become difficult
* Production stability suffers

A proper branching strategy provides:

* Stability
* Traceability
* Faster Releases
* Easier Rollbacks

---

# Real Project Branch Structure

```text
main
develop
feature/*
release/*
hotfix/*
```

---

# Understanding Each Branch

## Main Branch

Purpose:

```text
Production Ready Code
```

Rules:

* Direct commits not allowed
* Only reviewed code
* Always deployable

Example:

```text
main
```

contains:

```text
Production Version
```

---

## Develop Branch

Purpose:

```text
Integration Branch
```

All feature branches merge here first.

Example:

```text
main
   ↑
develop
```

---

## Feature Branch

Created for:

* New Features
* Enhancements
* Bug Fixes

Example:

```text
feature/login
feature/payment
feature/dashboard
```

Created from:

```bash
git checkout develop

git checkout -b feature/login
```

---

# Development Flow

```text
develop
      ↓
feature/login
      ↓
Development
      ↓
Pull Request
      ↓
Merge Back To develop
```

---

# Release Branch

## What is a Release Branch?

Release Branch is created when:

```text
Features Completed
Testing Started
```

Purpose:

Prepare production release without affecting ongoing development.

---

# Real-Time Scenario

Suppose:

```text
Version 2.0
```

contains:

* Login Feature
* Payment Feature
* Dashboard Feature

Development complete.

Create release branch:

```bash
git checkout develop

git checkout -b release/v2.0
```

---

# Why Release Branch?

While QA testing:

Developers continue:

```text
develop
```

for:

```text
Version 2.1
```

No interference.

---

# Release Flow

```text
develop
      ↓
release/v2.0
      ↓
QA Testing
      ↓
Bug Fixes
      ↓
Production Release
```

---

# Real-Time Example

Application release planned for Friday.

Create:

```bash
git checkout -b release/v2.0
```

QA performs:

* Functional Testing
* Regression Testing
* Security Testing
* Performance Testing

All fixes happen inside:

```text
release/v2.0
```

---

# After Release

Merge into:

```text
main
```

and

```text
develop
```

to keep branches synchronized.

---

# Git Tags

## What is a Tag?

Tag is used to mark important versions.

Think of it as:

```text
Snapshot Of Release
```

---

# Example

Release:

```text
Version 2.0
```

Create tag:

```bash
git tag -a v2.0 -m "Production Release v2.0"
```

Push:

```bash
git push origin v2.0
```

---

# Why Use Tags?

Tags help:

* Identify releases
* Rollback easily
* Track deployments

---

# Real-Time Scenario

Production currently running:

```text
v2.0
```

Need rollback.

Simply deploy:

```text
v1.9
```

tag.

---

# View Tags

```bash
git tag
```

Output:

```text
v1.0
v1.5
v2.0
```

---

# Show Tag Details

```bash
git show v2.0
```

---

# Production Rollback Using Tags

Suppose:

```text
v2.0
```

caused issues.

Rollback:

```bash
git checkout v1.9
```

Redeploy.

Production restored.

---

# Hotfix Branch

## What is a Hotfix Branch?

Used for:

```text
Critical Production Issues
```

that cannot wait for next release.

---

# Real-Time Scenario

Production issue:

```text
Users Cannot Login
```

Immediate action required.

---

# Create Hotfix Branch

From:

```text
main
```

Create:

```bash
git checkout main

git checkout -b hotfix/login-fix
```

---

# Why From Main?

Because:

```text
main = Current Production Code
```

Hotfix should be based on production.

---

# Hotfix Flow

```text
main
   ↓
hotfix/login-fix
   ↓
Fix
   ↓
Testing
   ↓
Production Deployment
```

---

# Example

Fix:

```text
Authentication Bug
```

Commit:

```bash
git add .

git commit -m "Fix authentication issue"
```

Merge:

```text
main
```

and

```text
develop
```

---

# Why Merge Back To Develop?

Otherwise:

Future releases may lose the hotfix.

---

# Real-Time Production Example

Issue:

```text
Payment Gateway Failure
```

Reported at:

```text
2 AM
```

Team created:

```text
hotfix/payment-fix
```

branch.

Fixed issue.

Deployed.

Merged fix back into:

```text
main
```

and

```text
develop
```

within one hour.

---

# Complete Branching Strategy

```text
                    feature/login
                   /
develop ----------- feature/payment
                   \
                    feature/dashboard

         ↓

release/v2.0

         ↓

main

         ↓

tag v2.0

         ↓

hotfix/login-fix
```

---

# Real Release Lifecycle

```text
Feature Development
        ↓
Merge Into develop
        ↓
Create Release Branch
        ↓
QA Testing
        ↓
Bug Fixes
        ↓
Merge Into main
        ↓
Create Tag
        ↓
Production Deployment
        ↓
Monitoring
        ↓
Hotfix If Needed
```

---

# Common Interview Questions

## Why Not Deploy Directly From Develop?

Because:

```text
Develop Contains Ongoing Work
```

Unfinished features may exist.

Release branch provides stability.

---

## Why Use Tags?

Tags provide:

* Version tracking
* Easy rollback
* Release auditing

---

## Why Create Hotfix Branch From Main?

Because:

```text
main = Current Production State
```

Fix must be based on actual production code.

---

## Why Merge Hotfix Into Develop?

To ensure future releases contain the production fix.

---

## How Do You Rollback Production?

Options:

### Using Previous Tag

```bash
git checkout v1.9
```

Redeploy.

---

### Using Git Revert

```bash
git revert <commit-id>
```

Create rollback commit.

---

# Example Interview Scenario

**Interviewer:** How do you manage production releases?

**Answer:**

"In our projects, developers create feature branches from develop. Once features are completed, they are merged into develop through pull requests. Before a release, we create a dedicated release branch where QA performs testing and bug fixes. After validation, the release branch is merged into main and tagged with a version such as v2.0. Production deployments are performed from tagged releases. If a critical issue occurs in production, we create a hotfix branch from main, implement the fix, deploy it, and then merge the hotfix back into both main and develop to keep all branches synchronized."

---

# Commands Summary

```bash
git checkout -b feature/login

git checkout -b release/v2.0

git checkout -b hotfix/login-fix

git tag -a v2.0 -m "Release v2.0"

git push origin v2.0

git tag

git show v2.0
```

---

# Interview Answer

> "In real projects, we follow a branching strategy using feature, develop, release, main, and hotfix branches. Feature branches are used for development, develop acts as the integration branch, release branches are created for testing and stabilization, main always contains production-ready code, and hotfix branches handle urgent production issues. We use Git tags to mark production releases and support quick rollbacks. This approach provides stability, traceability, and controlled production deployments."
