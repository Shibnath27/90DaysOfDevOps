# Day 39 – What is CI/CD?

Before building pipelines, it’s important to understand **why CI/CD exists** and the problems it solves in modern software development.

CI/CD helps teams **build, test, and deploy code automatically**, reducing human error and speeding up delivery.

---

# Task 1 – The Problem

Imagine a team of **5 developers** working on the same application and deploying manually.

### What can go wrong?

* Developers overwrite each other's code
* Integration conflicts appear late
* Manual deployment mistakes
* Inconsistent environments
* Long delays before bugs are discovered
* Production outages due to missed tests

Manual processes **do not scale well** as teams grow.

---

### What does "It works on my machine" mean?

This phrase occurs when:

* Code runs correctly on a developer's local system
* But **fails in another environment** (another developer machine, staging, or production)

Reasons include:

* Different OS versions
* Different dependency versions
* Missing environment variables
* Local configuration differences

CI pipelines solve this by **building and testing code in standardized environments**.

---

### How many times can a team safely deploy manually?

Typically:

* **1–2 deployments per day at most**

Manual deployment becomes risky when done frequently because:

* Human errors increase
* Testing is inconsistent
* Rollbacks are difficult

CI/CD allows teams to deploy **multiple times per day safely**.

---

# Task 2 – CI vs CD

## Continuous Integration (CI)

Continuous Integration is the practice of **frequently merging code changes into a shared repository**.

Every commit triggers automated processes such as:

* Building the application
* Running tests
* Checking code quality

Goal: **Catch integration problems early.**

**Example**

A developer pushes code → CI automatically runs:

```
Build → Unit Tests → Linting
```

If tests fail, the change is rejected.

---

## Continuous Delivery (CD)

Continuous Delivery extends CI by ensuring that **the application is always ready for deployment**.

After CI finishes successfully:

* The application is packaged
* Artifacts are created
* Deployment to production still requires **manual approval**

**Example**

Pipeline:

```
Build → Test → Package → Deploy to Staging → Ready for Production
```

A release manager clicks **"Deploy to Production"**.

---

## Continuous Deployment

Continuous Deployment goes one step further.

If all tests pass, the system **automatically deploys to production without human approval**.

**Example**

```
Push Code → Build → Test → Deploy to Production
```

Companies like **Netflix or Amazon** deploy this way.

---

# Task 3 – Pipeline Anatomy

## Trigger

The event that starts a pipeline.

Examples:

* Git push
* Pull request
* Scheduled job
* Manual trigger

Example:

```
on: push
```

---

## Stage

A logical phase in the pipeline.

Examples:

```
Build
Test
Deploy
```

Stages organize the pipeline workflow.

---

## Job

A collection of steps executed on the same machine.

Example:

```
job: build-app
```

A stage may contain **multiple jobs running in parallel**.

---

## Step

A single action executed inside a job.

Example:

```
- install dependencies
- run tests
- build application
```

Steps are the **smallest unit of work**.

---

## Runner

The machine that executes pipeline jobs.

Examples:

* GitHub-hosted runners
* Self-hosted runners
* CI servers

Example environment:

```
Ubuntu VM
Docker container
Kubernetes runner
```

---

## Artifact

A file or package produced by a job that can be used later.

Examples:

* compiled binaries
* Docker images
* test reports
* deployment packages

Example:

```
myapp.tar.gz
docker-image:latest
```

---

# Task 4 – CI/CD Pipeline Diagram

Example pipeline for:

Developer pushes code → test → build Docker image → deploy to staging.

```
Developer Push
      │
      ▼
Trigger (GitHub Push)
      │
      ▼
┌───────────────┐
│  Stage 1      │
│  Build        │
│  - install deps
│  - compile app
└───────────────┘
      │
      ▼
┌───────────────┐
│  Stage 2      │
│  Test         │
│  - unit tests
│  - lint checks
└───────────────┘
      │
      ▼
┌───────────────┐
│  Stage 3      │
│  Package      │
│  - build Docker image
│  - push to registry
└───────────────┘
      │
      ▼
┌───────────────┐
│  Stage 4      │
│  Deploy       │
│  - deploy to staging
└───────────────┘
```

---

# Task 5 – Explore in the Wild

Example repository explored:

Kubernetes GitHub repository

Folder:

```
.github/workflows/
```

Example workflow file:

```
ci.yml
```

### What triggers it?

Typical triggers:

```
on:
  push:
  pull_request:
```

Meaning the pipeline runs when:

* code is pushed
* pull requests are opened

---

### How many jobs?

Common jobs include:

* build
* test
* lint
* security scan

Large projects may have **10+ jobs running in parallel**.

---

### What does it do?

Typical actions:

* compile the project
* run automated tests
* check formatting
* run security scans
* validate pull requests before merging

This ensures **every contribution meets project standards** before entering the main branch.

---

# Key Takeaways

CI/CD enables:

* faster development cycles
* automated testing
* safer deployments
* consistent environments

Without CI/CD, modern large-scale software development would be **slow, risky, and inefficient**.

