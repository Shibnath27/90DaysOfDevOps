# Day 41 – Triggers & Matrix Builds

Today I explored different ways to trigger GitHub Actions workflows and how to run jobs across multiple environments.

---
# 1️⃣ Workflow: Pull Request Trigger

File: `.github/workflows/pr-check.yml`

```yaml
name: PR Check

on:
  pull_request:
    branches: [main]

jobs:
  pr-check:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Print PR branch
        run: echo "PR check running for branch: ${{ github.head_ref }}"
```

### Test Steps

1. Create a new branch

```
git checkout -b feature-test
```

2. Push the branch

```
git push origin feature-test
```

3. Open a Pull Request to `main`

The workflow will automatically run and appear in the **PR checks section**.

---

# 2️⃣ Workflow: Manual Trigger

File: `.github/workflows/manual.yml`

```yaml
name: Manual Workflow

on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Choose environment"
        required: true
        default: "staging"

jobs:
  manual-run:
    runs-on: ubuntu-latest

    steps:
      - name: Print selected environment
        run: echo "Deployment target: ${{ github.event.inputs.environment }}"
```

### How to run it

1. Open **Actions tab**
2. Select **Manual Workflow**
3. Click **Run workflow**
4. Enter value: `staging` or `production`

---

# 3️⃣ Workflow: Scheduled Trigger

File: `.github/workflows/schedule.yml`

```yaml
name: Nightly Job

on:
  schedule:
    - cron: '0 0 * * *'

jobs:
  scheduled-job:
    runs-on: ubuntu-latest

    steps:
      - name: Print scheduled run
        run: echo "Scheduled workflow running"
```

### Cron meaning

```
0 0 * * *
```

Runs **every day at midnight (UTC)**.

### Cron expression for every Monday at 9 AM

```
0 9 * * 1
```

---

# 4️⃣ Workflow: Matrix Build

File: `.github/workflows/matrix.yml`

```yaml
name: Matrix Build

on:
  push:

jobs:
  matrix-job:
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest]
        python-version: ["3.10", "3.11", "3.12"]

        exclude:
          - os: windows-latest
            python-version: "3.10"

    runs-on: ${{ matrix.os }}

    steps:
      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Show Python Version
        run: python --version

      - name: Simulate failure
        run: exit 1
        if: matrix.python-version == '3.12'
```

---

# Matrix Calculation

Matrix values:

```
OS: 2
Python versions: 3
```

Total combinations:

```
2 × 3 = 6 jobs
```

But we excluded:

```
windows + python 3.10
```

So total jobs executed:

```
5 jobs
```

All run **in parallel**.

---

# 5️⃣ Fail Fast Behavior

Default behavior:
fail-fast: true

If one job fails, all other running matrix jobs stop.

Modified behavior:
fail-fast: false

All matrix jobs continue running even if one fails.

---

# Key Learning

GitHub Actions supports multiple workflow triggers including push, pull requests, scheduled runs, and manual execution.

Matrix builds allow testing across multiple environments simultaneously, which is essential for verifying compatibility across operating systems and runtime versions.


