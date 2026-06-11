# Day 01 - Understanding the .git Folder

## Why Should You Learn the .git Folder?

Most developers use Git every day:

```bash
git add .
git commit -m "feature added"
git push
```

But very few understand what happens behind the scenes.

During interviews, most candidates can explain Git commands. However, when asked questions like:

* What happens internally when you run `git commit`?
* Where does Git store commit history?
* How does Git recover deleted commits?
* What exactly is the staging area?

many struggle to answer.

Understanding the `.git` folder helps not only in interviews but also in real-world troubleshooting, branch recovery, CI/CD debugging, merge conflict analysis, and repository recovery.

---

# What is the .git Folder?

When we initialize a repository:

```bash
git init
```

Git creates a hidden directory called:

```bash
.git/
```

This folder is Git's internal database.

Everything Git knows about your repository is stored here:

* Commit history
* Branches
* Tags
* Staging information
* Logs
* References
* Configuration
* Hooks
* Repository metadata

Without the `.git` folder, Git cannot track your project.

Your project files may still exist, but Git loses all knowledge about commits, branches, history, and version control.

---

# Real-Time Interview Explanation

Whenever interviewers ask about Git internals, I explain Git as two separate parts:

### 1. Working Directory

Contains actual application code.

Example:

```text
app.py
Dockerfile
Jenkinsfile
terraform/
kubernetes/
```

### 2. Git Database (.git)

Contains Git's internal information:

```text
Commit History
Branches
Tags
Logs
Staging Area
Configuration
References
```

In simple terms:

```text
Working Directory
        +
Git Database (.git)
        =
Version Control System
```

---

# Structure of the .git Folder

```text
.git
├── hooks
├── logs
├── objects
├── refs
├── HEAD
├── index
├── config
├── COMMIT_EDITMSG
└── description
```

Let's understand each component using real-world examples.

---

# 1. hooks/

## What Are Git Hooks?

Hooks are scripts that Git automatically executes when specific events occur.

Think of hooks as automated quality gates.

Instead of depending on developers to remember checks, Git performs them automatically.

---

## Common Hooks

### pre-commit

Runs before a commit is created.

### pre-push

Runs before code is pushed.

### post-commit

Runs after commit creation.

---

## Real DevOps Example

In one project, every developer commit triggered:

```text
Developer Commit
       ↓
Code Formatting Check
       ↓
Unit Tests
       ↓
Secret Scan
       ↓
Sonar Validation
       ↓
Commit Allowed
```

If any validation failed:

```text
Commit Rejected
```

This prevented:

* Hardcoded passwords
* Failed unit tests
* Bad quality code
* Security vulnerabilities

from entering the repository.

---

## Interview Answer

Hooks help automate validations before code reaches the repository, ensuring quality and security standards are enforced automatically.

---

# 2. logs/

## What is logs Folder?

The logs folder stores Git activity history.

Think of it as Git's flight recorder.

Every movement is recorded.

Examples:

* Commit creation
* Branch switching
* Reset operations
* Rebase activities
* Checkout history

Stored under:

```text
.git/logs/
```

---

## Real Production Scenario

A developer accidentally executed:

```bash
git reset --hard HEAD~3
```

Three commits disappeared.

The developer thought all work was lost.

Fortunately Git maintains movement history.

We executed:

```bash
git reflog
```

Found the deleted commit SHA.

Created a new branch from it.

Recovered all code within minutes.

---

## Interview Answer

The logs folder stores Git activity history and enables recovery of deleted commits using reflog.

---

# 3. index (Git Staging Area)

## What is index?

The index is Git's staging area.

When we run:

```bash
git add app.py
```

Git stores information inside:

```text
.git/index
```

before creating a commit.

---

## Why Does Git Need a Staging Area?

Think of it like an online shopping cart.

You don't immediately purchase every item you view.

You first choose what should go into the cart.

Git works the same way.

---

## Real-Time Flow

```text
Working Directory
        ↓
git add
        ↓
Index (Staging Area)
        ↓
git commit
        ↓
Repository
```

---

## Real Project Example

A developer modified:

```text
app.py
db.py
config.py
```

Database changes were incomplete.

Only application changes were ready.

Instead of committing everything:

```bash
git add app.py
```

Only application code entered the staging area.

A clean commit was created without unfinished database changes.

This is why staging exists.

---

## Interview Answer

The index acts as a temporary staging area between the working directory and repository, allowing developers to selectively commit changes.

---

# 4. objects/

## Most Important Folder in Git

If I had to call one folder the brain of Git, it would be:

```text
.git/objects
```

This is Git's actual database.

Everything eventually ends up here.

---

## What Does It Store?

Git stores data as objects.

Main object types:

### Blob

Stores file content.

### Tree

Stores folder structure.

### Commit

Stores commit metadata.

### Tag

Stores release information.

---

## Internal Flow

Suppose we create:

```text
src/
 └── login.py
```

Git internally creates:

```text
login.py
      ↓
Blob Object
      ↓
Tree Object
      ↓
Commit Object
```

Stored inside:

```text
.git/objects
```

---

## Real Production Scenario

A feature branch was accidentally deleted.

The remote branch was also removed.

Everyone assumed the work was lost.

However, commit objects still existed inside Git's object database.

Using the commit SHA, we recreated the branch and recovered nearly two weeks of development effort.

## step1: reflog => To find commit sha of deleted 
1. **Analyze the Output**:
The output will display a list of actions with corresponding commit SHAs and messages. Each entry will look something like this:
    
    ```sql
    Copy
    abc1234 HEAD@{0}: commit: Your last commit message
    def5678 HEAD@{1}: checkout: moving from feature-branch to main
    ghi9012 HEAD@{2}: commit: Another commit message
    ```
    
    - The **SHA** (e.g., `abc1234`) is what you will use to recover the branch.
    - The **message** gives context about what action was taken, which can help you identify the correct commit.

## step2: git checkout -b <new-branch> <commit-SHA> => Create a New Branch at the Commit

## step3: git push origin <new-branch> => push the new branch to remote.

---

## Interview Answer

The objects folder is Git's internal database and stores the complete repository history including commits, files, directories, and tags.

---

# 5. HEAD

## What is HEAD?

HEAD is a pointer.

It tells Git:

> Where am I currently working?

Example:

```text
HEAD
 ↓
main
```

---

## Switching Branches

```bash
git checkout dev
```

Now:

```text
HEAD
 ↓
dev
```

---

## Real Project Example

A developer accidentally committed production changes on the wrong branch.

Investigation showed:

```text
HEAD → main
```

instead of:

```text
HEAD → feature/payment
```

Understanding HEAD immediately explained why the code appeared in the wrong branch.

---

## Interview Answer

HEAD is a pointer that identifies the currently checked-out branch or commit.

---

# 6. refs/

## What is refs Folder?

References store branch and tag pointers.

Structure:

```text
refs/
├── heads/
└── tags/
```

---

## Example

```text
refs/heads/main
```

Contains:

```text
Latest Commit SHA
```

---

## What Happens During Commit?

When we run:

```bash
git commit -m "new feature"
```

Git updates:

```text
refs/heads/main
```

with the latest commit SHA.

---

## Real-Time Scenario

A developer asked:

> How does Git know which commit belongs to the main branch?

Answer:

Git tracks branch positions through refs.

---

## Interview Answer

Refs maintain branch and tag pointers that track the latest commit positions.

---

# 7. config

## What is config?

Stores repository-specific Git settings.

View settings:

```bash
git config --list
```

---

## Common Configuration

```text
Username
Email
Remote URL
Merge Strategy
Pull Behavior
Default Branch
```

---

## Real Production Example

A developer noticed commits showing the wrong username.

Investigation:

```bash
git config user.name
```

The username was incorrectly configured.

After updating the configuration, future commits displayed the correct author details.

---

## Interview Answer

The config file stores repository-specific settings such as username, email, remotes, merge behavior, and other Git configurations.

---

# Complete Internal Flow of Git

Suppose a developer runs:

```bash
git add app.py
git commit -m "login feature added"
git push origin main
```

Internally Git performs:

```text
app.py modified
       ↓
git add
       ↓
Stored in Index
       ↓
git commit
       ↓
Objects Created
       ↓
Refs Updated
       ↓
HEAD Remains on Current Branch
       ↓
Logs Updated
       ↓
git push
       ↓
Remote Repository Updated
```

---

# Real Interview Closing Answer

If I explain Git internally, I describe it as a content-addressable database stored inside the `.git` directory.

The index stores staged changes.

Objects store repository history.

HEAD tracks the current branch.

Refs maintain commit locations.

Logs record repository activity and support recovery.

Hooks automate validations before code enters the repository.

Config stores repository settings.

Understanding these components has helped me troubleshoot accidental resets, branch deletions, CI/CD failures, merge issues, and repository recovery scenarios in real-world DevOps projects.

Most engineers know Git commands.

Engineers who understand the `.git` folder understand how Git actually works.
