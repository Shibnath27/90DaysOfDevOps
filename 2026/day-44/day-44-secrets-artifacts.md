# Day 44 – Secrets, Artifacts & Real Tests in CI

Today I worked on making CI pipelines more practical by adding security, persistence, and real test execution.

---
# 1️⃣ Secrets Handling

File: `.github/workflows/secrets.yml`

```yaml
name: Secrets Demo

on:
  push:

jobs:
  secrets-test:
    runs-on: ubuntu-latest

    steps:
      - name: Check if secret exists
        run: |
          if [ -z "${{ secrets.MY_SECRET_MESSAGE }}" ]; then
            echo "The secret is set: false"
          else
            echo "The secret is set: true"
          fi

      - name: Attempt to print secret (masked)
        run: echo "Secret value: ${{ secrets.MY_SECRET_MESSAGE }}"
```

### Expected behavior

* GitHub **masks secrets in logs** → you’ll see `***` instead of the real value.

---

# 2️⃣ Use Secrets as Environment Variables

File: `.github/workflows/env-secret.yml`

```yaml
name: Secret as Env Variable

on:
  push:

jobs:
  use-secret:
    runs-on: ubuntu-latest

    steps:
      - name: Use secret safely
        env:
          SECRET_MSG: ${{ secrets.MY_SECRET_MESSAGE }}
        run: |
          echo "Using secret internally"
          if [ -n "$SECRET_MSG" ]; then
            echo "Secret accessed successfully"
          fi
```

⚠️ Never hardcode secrets. Always inject via `secrets`.

Also add these in repo settings (for Day 45):

* `DOCKER_USERNAME`
* `DOCKER_TOKEN`

---

# 3️⃣ Upload Artifacts

File: `.github/workflows/upload-artifact.yml`

```yaml
name: Upload Artifact

on:
  push:

jobs:
  generate-artifact:
    runs-on: ubuntu-latest

    steps:
      - name: Create report file
        run: echo "Test report generated on $(date)" > report.txt

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: test-report
          path: report.txt
```

### After run

Go to **Actions → Workflow → Artifacts → Download**

---

# 4️⃣ Share Artifacts Between Jobs

File: `.github/workflows/artifact-share.yml`

```yaml
name: Artifact Sharing

on:
  push:

jobs:

  job1:
    runs-on: ubuntu-latest
    steps:
      - name: Create file
        run: echo "Shared data between jobs" > data.txt

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: shared-file
          path: data.txt

  job2:
    runs-on: ubuntu-latest
    needs: job1

    steps:
      - name: Download artifact
        uses: actions/download-artifact@v4
        with:
          name: shared-file

      - name: Read file
        run: cat data.txt
```

---

# 5️⃣ Run Real Tests (Example: Python Script)

Add a file in repo: `test_script.py`

```python
print("Running test script...")
exit(0)
```

Workflow: `.github/workflows/run-tests.yml`

```yaml
name: Run Tests

on:
  push:

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Run script
        run: python test_script.py
```

### Test failure scenario

Change:

```python
exit(1)
```

Pipeline → ❌ fails (red)

Fix it → ✅ passes (green)

---

# 6️⃣ Caching Dependencies

File: `.github/workflows/cache.yml`

```yaml
name: Cache Example

on:
  push:

jobs:
  cache-demo:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Cache pip dependencies
        uses: actions/cache@v4
        with:
          path: ~/.cache/pip
          key: pip-cache-${{ hashFiles('**/requirements.txt') }}

      - name: Install dependencies
        run: pip install -r requirements.txt || true
```

### What happens

* First run → installs everything
* Second run → pulls from cache → faster execution

---

# 7️⃣ Documentation File




# GitHub Secrets

Secrets are used to store sensitive data like passwords, API keys, and tokens.

They are encrypted and masked in logs.

When trying to print a secret, GitHub replaces it with ***.

Reason:
To prevent sensitive data leaks in CI logs.

---

# Using Secrets

Secrets can be passed as environment variables to steps.

They should never be hardcoded in workflow files.

Example use cases:

Docker login credentials  
API keys  
Cloud provider access tokens  

---

# Artifacts

Artifacts are files generated during a workflow that can be stored and downloaded later.

Example:

Test reports  
Logs  
Build outputs  

Artifacts persist beyond job execution.

---

# Sharing Artifacts Between Jobs

Jobs run on separate machines.

Artifacts allow files to be passed between jobs.

Example:
A build job creates a binary → test job downloads and tests it.

---

# Running Real Tests

A Python script was executed in the pipeline.

If the script exits with:

0 → success  
non-zero → failure  

Breaking the script caused the pipeline to fail.

Fixing it restored a successful run.

---

# Caching

Caching stores dependencies between runs.

Example:
Python packages stored in ~/.cache/pip

Benefits:

Faster pipeline execution  
Reduced network usage  

---

# Key Learning

CI pipelines are not just automation — they are:

Secure (using secrets)  
Persistent (using artifacts)  
Reliable (through real test execution)  
Efficient (through caching)  

