# Day 47 – Advanced Triggers in GitHub Actions

Today I explored advanced event-driven automation in CI/CD pipelines.

---

# 1️⃣ PR Lifecycle Workflow

File: `.github/workflows/pr-lifecycle.yml`

```yaml
name: PR Lifecycle Events

on:
  pull_request:
    types: [opened, synchronize, reopened, closed]

jobs:
  pr-events:
    runs-on: ubuntu-latest

    steps:
      - name: Print event info
        run: |
          echo "Event: ${{ github.event.action }}"
          echo "Title: ${{ github.event.pull_request.title }}"
          echo "Author: ${{ github.event.pull_request.user.login }}"
          echo "Source branch: ${{ github.head_ref }}"
          echo "Target branch: ${{ github.base_ref }}"

      - name: Run only if PR merged
        if: github.event.pull_request.merged == true
        run: echo "PR was merged ✅"
```

---

# 2️⃣ PR Validation Workflow (Real Gate)

File: `.github/workflows/pr-checks.yml`

```yaml
name: PR Checks

on:
  pull_request:
    branches: [main]

jobs:

  file-size-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check file sizes
        run: |
          for file in $(git ls-files); do
            size=$(stat -c%s "$file")
            if [ $size -gt 1048576 ]; then
              echo "File $file exceeds 1MB"
              exit 1
            fi
          done

  branch-name-check:
    runs-on: ubuntu-latest
    steps:
      - name: Validate branch name
        run: |
          BRANCH="${{ github.head_ref }}"
          if [[ ! "$BRANCH" =~ ^(feature|fix|docs)/ ]]; then
            echo "Invalid branch name: $BRANCH"
            exit 1
          fi

  pr-body-check:
    runs-on: ubuntu-latest
    steps:
      - name: Check PR description
        run: |
          BODY="${{ github.event.pull_request.body }}"
          if [ -z "$BODY" ]; then
            echo "Warning: PR description is empty"
          fi
```

---

# 3️⃣ Scheduled Workflows

File: `.github/workflows/scheduled-tasks.yml`

```yaml
name: Scheduled Tasks

on:
  schedule:
    - cron: '30 2 * * 1'
    - cron: '0 */6 * * *'
  workflow_dispatch:

jobs:
  scheduled-job:
    runs-on: ubuntu-latest

    steps:
      - name: Identify schedule
        run: echo "Triggered by schedule: ${{ github.event.schedule }}"

      - name: Health check
        run: |
          STATUS=$(curl -s -o /dev/null -w "%{http_code}" https://example.com)
          echo "HTTP Status: $STATUS"
```

### Cron answers

* Every weekday 9 AM IST → `30 3 * * 1-5` (UTC conversion)
* First day of month midnight → `0 0 1 * *`

### Why delays happen

* GitHub deprioritizes inactive repos
* Shared infrastructure load balancing

---

# 4️⃣ Path & Branch Filters

File: `.github/workflows/smart-triggers.yml`

```yaml
name: Smart Trigger

on:
  push:
    branches:
      - main
      - release/*
    paths:
      - 'src/**'
      - 'app/**'

jobs:
  run-if-code-changed:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Code change detected"
```

---

File: `.github/workflows/ignore-docs.yml`

```yaml
name: Ignore Docs Changes

on:
  push:
    paths-ignore:
      - '*.md'
      - 'docs/**'

jobs:
  skip-docs:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Triggered because non-doc files changed"
```

---

# 5️⃣ Workflow Chaining (workflow_run)

### tests.yml

```yaml
name: Run Tests

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running tests"
```

---

### deploy-after-tests.yml

```yaml
name: Deploy After Tests

on:
  workflow_run:
    workflows: ["Run Tests"]
    types: [completed]

jobs:
  deploy:
    runs-on: ubuntu-latest
    if: github.event.workflow_run.conclusion == 'success'

    steps:
      - name: Deploy step
        run: echo "Deploying because tests passed"

      - name: Handle failure
        if: github.event.workflow_run.conclusion != 'success'
        run: echo "Tests failed, skipping deploy"
```

---

# 6️⃣ External Trigger (repository_dispatch)

File: `.github/workflows/external-trigger.yml`

```yaml
name: External Trigger

on:
  repository_dispatch:
    types: [deploy-request]

jobs:
  external:
    runs-on: ubuntu-latest

    steps:
      - name: Print payload
        run: echo "Environment: ${{ github.event.client_payload.environment }}"
```

### Trigger command

```bash
gh api repos/<owner>/<repo>/dispatches \
  -f event_type=deploy-request \
  -f client_payload='{"environment":"production"}'
```

---

# 7️⃣ Documentation File


# Pull Request Lifecycle

Workflows can respond to PR events:

opened  
synchronize  
reopened  
closed  

This allows automation at every stage of a PR lifecycle.

---

# PR Validation

Implemented checks for:

File size limits  
Branch naming conventions  
PR description validation  

These act as quality gates before merging.

---

# Scheduled Workflows

Cron-based triggers allow periodic automation.

Examples:

Every Monday at 2:30 UTC  
Every 6 hours  

Used for:

Health checks  
Maintenance jobs  
Scheduled builds  

---

# Path Filters

paths → run only when specific files change  
paths-ignore → skip runs for certain files  

This reduces unnecessary pipeline executions.

---

# Workflow Chaining

Using workflow_run, workflows can trigger other workflows.

Example:

Test workflow → Deploy workflow  

This enables multi-stage pipelines.

---

# External Triggers

repository_dispatch allows external systems to trigger workflows.

Examples:

Monitoring tools  
Slack bots  
Deployment systems  

---

# Key Learning

CI/CD pipelines are event-driven systems.

They can respond to:

Code changes  
Pull requests  
Schedules  
External systems  

This enables building fully automated DevOps pipelines.
