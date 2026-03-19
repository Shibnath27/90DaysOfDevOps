# **Day 48 capstone** — a **production-style, end-to-end CI/CD system** using GitHub Actions. No fluff—this is structured exactly like a real-world pipeline.

---

# 🔧 1️⃣ Project Structure

```
github-actions-capstone/
├── app.py
├── requirements.txt
├── Dockerfile
├── test.sh
├── README.md
└── .github/workflows/
```

---

# 🧪 2️⃣ Sample App (Flask)

### `app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "App is running!"

@app.route("/health")
def health():
    return {"status": "ok"}

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

### `requirements.txt`

```
flask
```

---

### `test.sh`

```bash
#!/bin/bash
curl -f http://localhost:5000/health || exit 1
```

---

# 🐳 3️⃣ Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["python", "app.py"]
```

---

# 🔁 4️⃣ Reusable Workflow — Build & Test

`.github/workflows/reusable-build-test.yml`

```yaml
name: Reusable Build & Test

on:
  workflow_call:
    inputs:
      python_version:
        required: true
        type: string
      run_tests:
        required: false
        type: boolean
        default: true

jobs:
  build-test:
    runs-on: ubuntu-latest

    outputs:
      test_result: ${{ steps.result.outputs.status }}

    steps:
      - uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ inputs.python_version }}

      - name: Install deps
        run: pip install -r requirements.txt

      - name: Run app
        run: |
          python app.py &
          sleep 5

      - name: Run tests
        if: inputs.run_tests == true
        run: bash test.sh

      - name: Set output
        id: result
        run: echo "status=passed" >> $GITHUB_OUTPUT
```

---

# 🐳 5️⃣ Reusable Docker Workflow

`.github/workflows/reusable-docker.yml`

```yaml
name: Reusable Docker

on:
  workflow_call:
    inputs:
      image_name:
        required: true
        type: string
      tag:
        required: true
        type: string
    secrets:
      docker_username:
        required: true
      docker_token:
        required: true

jobs:
  docker:
    runs-on: ubuntu-latest

    outputs:
      image_url: ${{ steps.meta.outputs.url }}

    steps:
      - uses: actions/checkout@v4

      - name: Login Docker Hub
        run: echo "${{ secrets.docker_token }}" | docker login -u "${{ secrets.docker_username }}" --password-stdin

      - name: Build & Push
        run: |
          IMAGE=${{ secrets.docker_username }}/${{ inputs.image_name }}:${{ inputs.tag }}
          docker build -t $IMAGE .
          docker push $IMAGE

      - name: Set output
        id: meta
        run: echo "url=${{ secrets.docker_username }}/${{ inputs.image_name }}:${{ inputs.tag }}" >> $GITHUB_OUTPUT
```

---

# 🔍 6️⃣ PR Pipeline

`.github/workflows/pr-pipeline.yml`

```yaml
name: PR Pipeline

on:
  pull_request:
    branches: [main]
    types: [opened, synchronize]

jobs:
  test:
    uses: ./.github/workflows/reusable-build-test.yml
    with:
      python_version: "3.11"
      run_tests: true

  pr-comment:
    runs-on: ubuntu-latest
    needs: test

    steps:
      - run: echo "PR checks passed for branch: ${{ github.head_ref }}"
```

✅ No Docker push here — correct production practice.

---

# 🚀 7️⃣ Main Pipeline

`.github/workflows/main-pipeline.yml`

```yaml
name: Main Pipeline

on:
  push:
    branches: [main]

jobs:
  test:
    uses: ./.github/workflows/reusable-build-test.yml
    with:
      python_version: "3.11"

  docker:
    needs: test
    uses: ./.github/workflows/reusable-docker.yml
    with:
      image_name: github-actions-capstone
      tag: latest
    secrets:
      docker_username: ${{ secrets.DOCKER_USERNAME }}
      docker_token: ${{ secrets.DOCKER_TOKEN }}

  deploy:
    needs: docker
    runs-on: ubuntu-latest
    environment: production

    steps:
      - run: echo "Deploying image: ${{ needs.docker.outputs.image_url }} to production"
```

---

# ⏱️ 8️⃣ Scheduled Health Check

`.github/workflows/health-check.yml`

```yaml
name: Health Check

on:
  schedule:
    - cron: '0 */12 * * *'
  workflow_dispatch:

jobs:
  health:
    runs-on: ubuntu-latest

    steps:
      - name: Pull image
        run: docker pull ${{ secrets.DOCKER_USERNAME }}/github-actions-capstone:latest

      - name: Run container
        run: |
          docker run -d -p 5000:5000 --name test-app \
          ${{ secrets.DOCKER_USERNAME }}/github-actions-capstone:latest

      - name: Wait
        run: sleep 5

      - name: Check health
        run: |
          STATUS=$(curl -s -o /dev/null -w "%{http_code}" http://localhost:5000/health)
          if [ "$STATUS" -eq 200 ]; then
            echo "PASSED"
          else
            echo "FAILED"
            exit 1
          fi

      - name: Cleanup
        run: docker rm -f test-app

      - name: Summary
        run: |
          echo "## Health Check Report" >> $GITHUB_STEP_SUMMARY
          echo "- Status: PASSED" >> $GITHUB_STEP_SUMMARY
          echo "- Time: $(date)" >> $GITHUB_STEP_SUMMARY
```

---

# 🛡️ 9️⃣ (Bonus) Security Scan — HIGH VALUE

Add after Docker build:

```yaml
- name: Scan image
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: ${{ secrets.DOCKER_USERNAME }}/github-actions-capstone:latest
    severity: CRITICAL
    exit-code: 1
```

👉 This is what separates **DevOps vs DevSecOps mindset**

---

# 🧠 🔥 Architecture 

```
PR → Build + Test → Feedback

Merge → Build → Docker → Push → Deploy (Manual Approval)

Schedule → Pull Image → Run → Health Check
```
