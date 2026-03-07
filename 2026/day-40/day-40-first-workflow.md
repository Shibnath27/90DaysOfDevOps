# Day 40 – First GitHub Actions Workflow

## Goal
Create and run a basic CI pipeline using GitHub Actions.

This exercise demonstrates how code pushed to a repository can automatically trigger a workflow in the cloud.

---

# Repository Setup

Repository name:
github-actions-practice

Folder structure used for workflows:

```
.github/
   └── workflows/
        └── hello.yml
```
---


# 1️⃣ Workflow File

Location: `.github/workflows/hello.yml`

```yaml
name: Hello Workflow

on: 
  push:

jobs:
  greet:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Print greeting
        run: echo "Hello from GitHub Actions!"

      - name: Print date and time
        run: date

      - name: Print branch name
        run: echo "Branch name is ${{ github.ref_name }}"

      - name: List files in repository
        run: ls -la

      - name: Show runner operating system
        run: echo "Running on $RUNNER_OS"
```

Push this file and then open the **Actions tab in the GitHub repository** to see the workflow run.





---

# Screenshot

Add a screenshot of your **green successful workflow run** from the Actions tab.

Example:

<img width="1362" height="552" alt="image" src="https://github.com/user-attachments/assets/78cd518e-7b3e-4504-8465-a53784049c0b" />


<img width="1343" height="605" alt="image" src="https://github.com/user-attachments/assets/b4a81289-33e0-4155-92be-b2006854e1bb" />


---

# Understanding the Workflow Keys

### on:

Defines the event that triggers the workflow.
In this workflow, the pipeline runs every time code is pushed to the repository.

### jobs:

Defines the set of jobs that will run in the pipeline.

### runs-on:

Specifies the environment used to execute the job.
Here it runs on an Ubuntu Linux runner.

### steps:

A sequence of individual tasks that the job performs.

### uses:

Runs a pre-built GitHub Action.
`actions/checkout` downloads the repository code onto the runner.

### run:

Executes shell commands directly in the runner environment.

### name:

A descriptive label for the step, shown in the workflow logs.

---

# Pipeline Execution Steps

1. Repository code is checked out
2. A greeting message is printed
3. Current date and time are displayed
4. Branch name triggering the workflow is shown
5. Files in the repository are listed
6. Runner operating system is displayed

---

# Breaking the Pipeline (Testing Failure)

A failing command was added:

```yaml
- name: Fail step
  run: exit 1
```

Result:

<img width="1366" height="613" alt="image" src="https://github.com/user-attachments/assets/1eca0751-3514-47ab-9e20-b0f37801a4d4" />


• Workflow run failed
• The failing step was marked in red
• Logs displayed the error and exit code

After removing the failing command and pushing again, the pipeline ran successfully.

---

# What a Failed Pipeline Looks Like

When a pipeline fails:

* GitHub marks the workflow as **failed**
* The failing step is highlighted
* Logs show the exact command and error message
* This helps quickly identify and debug issues

---

# Key Learning

GitHub Actions allows developers to automate tasks such as building, testing, and deploying applications whenever code changes are pushed to the repository.

This workflow demonstrates the basic structure of a CI pipeline.


