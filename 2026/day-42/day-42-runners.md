# Day 42 – GitHub Runners

Today I learned how CI/CD jobs are executed using runners.

A runner is the machine that executes workflow jobs.

---
# 1️⃣ Workflow – GitHub Hosted Runners

File: `.github/workflows/multi-os.yml`

This runs **3 jobs in parallel on different operating systems**.

```yaml
name: Multi OS Runner Test

on:
  push:

jobs:

  ubuntu-job:
    runs-on: ubuntu-latest
    steps:
      - name: Print OS
        run: echo "Running on Ubuntu"

      - name: Show hostname
        run: hostname

      - name: Show current user
        run: whoami

  windows-job:
    runs-on: windows-latest
    steps:
      - name: Print OS
        run: echo Running on Windows

      - name: Show hostname
        run: hostname

      - name: Show current user
        run: whoami

  macos-job:
    runs-on: macos-latest
    steps:
      - name: Print OS
        run: echo "Running on macOS"

      - name: Show hostname
        run: hostname

      - name: Show current user
        run: whoami
```

### What you will see

Three **parallel jobs**:

```
ubuntu-job
windows-job
macos-job
```

---

# 2️⃣ Workflow – Check Preinstalled Tools

File: `.github/workflows/tools-check.yml`

```yaml
name: Runner Tools Check

on:
  push:

jobs:
  check-tools:
    runs-on: ubuntu-latest

    steps:
      - name: Check Docker
        run: docker --version

      - name: Check Python
        run: python --version

      - name: Check Node
        run: node --version

      - name: Check Git
        run: git --version
```

### Why pre-installed tools matter

Because CI pipelines start **faster** and avoid installing dependencies every run.

---

# 3️⃣ Setup Self-Hosted Runner

Go to your repository:

```
Settings
↓
Actions
↓
Runners
↓
New self-hosted runner
```

Choose **Linux**.

GitHub will give commands similar to this:

```bash
mkdir actions-runner && cd actions-runner

curl -o actions-runner-linux-x64.tar.gz -L https://github.com/actions/runner/releases/download/v2.317.0/actions-runner-linux-x64-2.317.0.tar.gz

tar xzf ./actions-runner-linux-x64.tar.gz
```

Configure the runner:

```bash
./config.sh --url https://github.com/YOUR_USERNAME/github-actions-practice --token YOUR_TOKEN
```

Start it:

```bash
./run.sh
```

If successful, the runner will show in GitHub with a **green dot (Idle)**.

---

# 4️⃣ Workflow Using Self-Hosted Runner

File: `.github/workflows/self-hosted.yml`

```yaml
name: Self Hosted Runner Test

on:
  workflow_dispatch:

jobs:
  test-runner:
    runs-on: self-hosted

    steps:
      - name: Show hostname
        run: hostname

      - name: Show working directory
        run: pwd

      - name: Create test file
        run: echo "Runner test file" > runner-test.txt
```

### Verification

After the job runs:

Check your machine:

```
runner-test.txt
```

The file will exist in the runner working directory.

---

# 5️⃣ Using Labels

Add a label in the runner settings:

```
my-linux-runner
```

Update workflow:

```yaml
runs-on: [self-hosted, my-linux-runner]
```

This ensures only that runner executes the job.

---

# 6️⃣ Documentation File

# GitHub Hosted Runners

GitHub provides managed virtual machines that run workflow jobs.

Examples:

ubuntu-latest  
windows-latest  
macos-latest  

These runners start automatically when a workflow begins.

GitHub manages the infrastructure and software environment.

---

# Pre-installed Tools

On ubuntu-latest runner I verified these tools:

Docker
Python
Node.js
Git

These tools are already installed which speeds up CI pipelines.

---

# Self Hosted Runner

A self-hosted runner is a machine that you control.

It can run on:

Local machine
Cloud VM
Dedicated server

I installed a runner on my system and connected it to my repository.

Once started, the runner appeared in GitHub with an Idle status.

---

# Workflow Using Self Hosted Runner

The workflow used:

runs-on: self-hosted

The job executed on my own machine and created a file locally.

This confirmed the pipeline was running on my hardware.

---

# Runner Labels

Labels help target specific runners.

Example:

runs-on: [self-hosted, my-linux-runner]

This ensures only runners with that label pick up the job.

Labels are useful when multiple runners exist with different capabilities.

---

# GitHub Hosted vs Self Hosted

GitHub Hosted
Who manages it: GitHub
Cost: Included with usage limits
Pre-installed tools: Yes
Good for: Standard CI pipelines
Security concern: Less control over environment

Self Hosted
Who manages it: You
Cost: Your infrastructure cost
Pre-installed tools: You manage them
Good for: Custom environments and private infrastructure
Security concern: Must secure the machine yourself


