# Day 46 – Reusable Workflows & Composite Actions

Today I learned how to avoid duplication in CI/CD pipelines by creating reusable workflows and composite actions.

---


# 1️⃣ Reusable Workflow

File: `.github/workflows/reusable-build.yml`

```yaml
name: Reusable Build Workflow

on:
  workflow_call:
    inputs:
      app_name:
        required: true
        type: string
      environment:
        required: true
        type: string
        default: staging
    secrets:
      docker_token:
        required: true

jobs:
  build:
    runs-on: ubuntu-latest

    outputs:
      build_version: ${{ steps.version.outputs.build_version }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Print build info
        run: |
          echo "Building ${{ inputs.app_name }} for ${{ inputs.environment }}"

      - name: Verify secret
        run: |
          if [ -n "${{ secrets.docker_token }}" ]; then
            echo "Docker token is set: true"
          else
            echo "Docker token is set: false"
          fi

      - name: Generate version
        id: version
        run: |
          SHORT_SHA=${GITHUB_SHA::7}
          VERSION="v1.0-${SHORT_SHA}"
          echo "build_version=$VERSION" >> $GITHUB_OUTPUT
```

---

# 2️⃣ Caller Workflow

File: `.github/workflows/call-build.yml`

```yaml
name: Call Reusable Workflow

on:
  push:
    branches:
      - main

jobs:
  build:
    uses: ./.github/workflows/reusable-build.yml
    with:
      app_name: "my-web-app"
      environment: "production"
    secrets:
      docker_token: ${{ secrets.DOCKER_TOKEN }}

  print-version:
    runs-on: ubuntu-latest
    needs: build

    steps:
      - name: Print build version
        run: echo "Build version is ${{ needs.build.outputs.build_version }}"
```

### What happens

* Caller triggers reusable workflow
* Reusable workflow runs
* Output (`build_version`) passed back
* Second job prints it

---

# 3️⃣ Composite Action

File: `.github/actions/setup-and-greet/action.yml`

```yaml
name: Setup and Greet
description: Custom composite action to greet user

inputs:
  name:
    required: true
  language:
    required: false
    default: en

outputs:
  greeted:
    value: true

runs:
  using: "composite"
  steps:
    - name: Greeting
      shell: bash
      run: |
        if [ "${{ inputs.language }}" == "en" ]; then
          echo "Hello, ${{ inputs.name }}!"
        else
          echo "Hi, ${{ inputs.name }}!"
        fi

    - name: Show system info
      shell: bash
      run: |
        echo "Date: $(date)"
        echo "OS: $(uname -s)"
```

---

# 4️⃣ Workflow Using Composite Action

File: `.github/workflows/composite-test.yml`

```yaml
name: Composite Action Test

on:
  push:

jobs:
  greet:
    runs-on: ubuntu-latest

    steps:
      - name: Run custom action
        uses: ./.github/actions/setup-and-greet
        with:
          name: "Shibnath"
          language: "en"
```

---

# 5️⃣ Documentation File


# Reusable Workflow

A reusable workflow is a workflow that can be called by another workflow.

It uses the `workflow_call` trigger.

It allows sharing common pipeline logic across repositories or workflows.

Reusable workflows must be stored in:
.github/workflows/

---

# Caller Workflow

A caller workflow invokes a reusable workflow using:

jobs:
  build:
    uses: ./.github/workflows/reusable-build.yml

Inputs and secrets can be passed to the reusable workflow.

---

# Outputs

Reusable workflows can return outputs.

These outputs can be used by other jobs in the caller workflow.

Example:
A build workflow generates a version → deploy workflow uses it.

---

# Composite Action

A composite action is a custom action made of multiple steps.

It is defined using:

runs:
  using: "composite"

It is called inside a step using `uses:`.

---

# Reusable Workflow vs Composite Action

Reusable Workflow
Triggered by: workflow_call  
Can contain jobs: Yes  
Can contain multiple steps: Yes  
Lives in: .github/workflows/  
Can accept secrets: Yes  
Best for: Full pipeline logic  

Composite Action
Triggered by: uses: in a step  
Can contain jobs: No  
Can contain multiple steps: Yes  
Lives in: .github/actions/  
Can accept secrets: Indirectly via inputs  
Best for: Reusable step logic  

---

# Key Learning

Reusable workflows help standardize CI/CD pipelines across projects.

Composite actions help reduce duplication at the step level.

Both are essential for building scalable DevOps systems.
