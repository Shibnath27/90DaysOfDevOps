# Day 43 – Jobs, Steps, Environment Variables & Conditionals

Today I learned how to control the execution flow of CI/CD pipelines using GitHub Actions.

---

# 1️⃣ Multi-Job Workflow

File: `.github/workflows/multi-job.yml`

This demonstrates **job dependencies**.

```yaml
name: Multi Job Pipeline

on:
  push:

jobs:

  build:
    runs-on: ubuntu-latest
    steps:
      - name: Build Step
        run: echo "Building the app"

  test:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Test Step
        run: echo "Running tests"

  deploy:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - name: Deploy Step
        run: echo "Deploying"
```

### Pipeline Order

```
build → test → deploy
```

In the **Actions workflow graph**, you will see the dependency chain.

---

# 2️⃣ Environment Variables

File: `.github/workflows/env-vars.yml`

```yaml
name: Environment Variables Demo

on:
  push:

env:
  APP_NAME: myapp

jobs:

  env-demo:
    runs-on: ubuntu-latest

    env:
      ENVIRONMENT: staging

    steps:
      - name: Print Variables
        env:
          VERSION: 1.0.0
        run: |
          echo "App Name: $APP_NAME"
          echo "Environment: $ENVIRONMENT"
          echo "Version: $VERSION"

      - name: Print GitHub Context
        run: |
          echo "Commit SHA: ${{ github.sha }}"
          echo "Triggered by: ${{ github.actor }}"
```

---

# 3️⃣ Job Outputs

File: `.github/workflows/job-output.yml`

```yaml
name: Job Outputs Example

on:
  push:

jobs:

  generate-date:
    runs-on: ubuntu-latest

    outputs:
      today: ${{ steps.date_step.outputs.today }}

    steps:
      - name: Generate date
        id: date_step
        run: echo "today=$(date)" >> $GITHUB_OUTPUT

  print-date:
    runs-on: ubuntu-latest
    needs: generate-date

    steps:
      - name: Print received output
        run: echo "Date from previous job: ${{ needs.generate-date.outputs.today }}"
```

### Why pass outputs?

Outputs allow **data sharing between isolated jobs**, which run on different runners.

Example uses:

* Passing build artifacts version
* Passing container image tags
* Passing test results

---

# 4️⃣ Conditionals

File: `.github/workflows/conditionals.yml`

```yaml
name: Conditional Steps

on:
  push:
  pull_request:

jobs:

  conditional-demo:
    runs-on: ubuntu-latest

    steps:
      - name: Run only on main branch
        if: github.ref == 'refs/heads/main'
        run: echo "This runs only on main"

      - name: Force failure
        run: exit 1
        continue-on-error: true

      - name: Run if previous step failed
        if: failure()
        run: echo "Previous step failed but pipeline continued"

  push-only-job:
    if: github.event_name == 'push'
    runs-on: ubuntu-latest

    steps:
      - run: echo "This job runs only on push events"
```

### What `continue-on-error: true` does

The step **fails but the job continues executing**.

---

# 5️⃣ Smart Pipeline

File: `.github/workflows/smart-pipeline.yml`

```yaml
name: Smart Pipeline

on:
  push:

jobs:

  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Lint code
        run: echo "Running lint checks"

  test:
    runs-on: ubuntu-latest
    steps:
      - name: Run tests
        run: echo "Executing tests"

  summary:
    runs-on: ubuntu-latest
    needs: [lint, test]

    steps:
      - name: Print branch type
        run: |
          if [[ "${{ github.ref_name }}" == "main" ]]; then
            echo "Main branch push detected"
          else
            echo "Feature branch push detected"
          fi

      - name: Print commit message
        run: echo "Commit message: ${{ github.event.head_commit.message }}"
```

### Pipeline Flow

```
        lint
          │
          │
        test
          │
          ▼
        summary
```

`lint` and `test` run **in parallel**, then `summary` runs after both complete.

---

# 6️⃣ Documentation File

# Multi Job Workflows

A workflow can contain multiple jobs.

Example pipeline:

build → test → deploy

Jobs can depend on other jobs using the `needs` keyword.

This ensures sequential execution.

---

# Environment Variables

Environment variables can be defined at different levels.

Workflow level:
env:
  APP_NAME: myapp

Job level:
ENVIRONMENT: staging

Step level:
VERSION: 1.0.0

These variables can be accessed using `$VARIABLE_NAME`.

---

# GitHub Context Variables

GitHub provides metadata about the workflow run.

Examples used:

github.sha → commit SHA  
github.actor → user who triggered the workflow  

These values are useful for logging and debugging.

---

# Job Outputs

Jobs run on separate runners and cannot share variables directly.

Outputs allow passing data between jobs.

Example:
A build job can generate a version number which is later used by a deploy job.

---

# Conditionals

Conditionals allow workflows to run specific steps only under certain conditions.

Examples implemented:

Run step only on main branch  
Run job only for push events  
Run step if a previous step failed  

---

# Continue On Error

`continue-on-error: true`

Allows a step to fail without stopping the entire job.

This is useful for optional checks.

---

# Smart Pipeline

A smart pipeline was created where:

lint and test jobs run in parallel  
summary job runs after both jobs finish  

The summary job prints whether the push came from the main branch or a feature branch.

---

# Key Learning

CI/CD pipelines are not just sequences of commands.

They can be controlled using dependencies, environment variables, outputs, and conditionals to build complex automation workflows.


