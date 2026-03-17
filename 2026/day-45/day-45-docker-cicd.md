# Day 45 – Docker Build & Push using GitHub Actions

Today I built a complete CI/CD pipeline that automatically builds and pushes a Docker image to Docker Hub.

---

# 1️⃣ Minimal Dockerfile (if you don’t already have one)

Place in repo root:

```dockerfile
# Simple Python app example
FROM python:3.11-slim

WORKDIR /app

COPY . .

CMD ["python", "test_script.py"]
```

(You can replace with your Day 36 app.)

---

# 2️⃣ Full CI/CD Workflow

File: `.github/workflows/docker-publish.yml`

```yaml
name: Docker Build & Push

on:
  push:
    branches:
      - main
      - '**'   # allow builds on all branches (push restricted later)

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      # Generate short SHA
      - name: Extract short SHA
        id: vars
        run: echo "SHORT_SHA=${GITHUB_SHA::7}" >> $GITHUB_ENV

      # Build image
      - name: Build Docker image
        run: |
          docker build -t ${{ secrets.DOCKER_USERNAME }}/github-actions-practice:latest .

      # Login to Docker Hub
      - name: Login to Docker Hub
        if: github.ref == 'refs/heads/main'
        run: echo "${{ secrets.DOCKER_TOKEN }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      # Tag image
      - name: Tag image
        if: github.ref == 'refs/heads/main'
        run: |
          docker tag ${{ secrets.DOCKER_USERNAME }}/github-actions-practice:latest \
          ${{ secrets.DOCKER_USERNAME }}/github-actions-practice:sha-${SHORT_SHA}

      # Push images
      - name: Push images
        if: github.ref == 'refs/heads/main'
        run: |
          docker push ${{ secrets.DOCKER_USERNAME }}/github-actions-practice:latest
          docker push ${{ secrets.DOCKER_USERNAME }}/github-actions-practice:sha-${SHORT_SHA}
```

---

# 3️⃣ What This Pipeline Does

### On every push:

* Builds Docker image ✅

### On **main branch only**:

* Logs into Docker Hub 🔐
* Tags image:

  * `latest`
  * `sha-xxxxxxx`
* Pushes both 🚀

---

# 4️⃣ Verify on Docker Hub

Go to:

```
https://hub.docker.com/r/<your-username>/github-actions-practice
```

You should see:

```
latest
sha-abc1234
```

---

# 5️⃣ Add Status Badge

In your `README.md`:

```markdown
![Docker Build](https://github.com/<your-username>/github-actions-practice/actions/workflows/docker-publish.yml/badge.svg)
```

Replace `<your-username>`.

---

# 6️⃣ Pull & Run the Image

On your machine:

```bash
docker pull <your-username>/github-actions-practice:latest

docker run <your-username>/github-actions-practice:latest
```

If your script runs → pipeline is fully working ✅

---

# 7️⃣ Documentation File


# Workflow Overview

Trigger:
Push to repository

Steps:

1. Checkout code
2. Build Docker image
3. Generate short commit SHA
4. Login to Docker Hub (only on main)
5. Tag image with:
   - latest
   - sha-<commit>
6. Push image to Docker Hub

---

# Image Tags

latest → most recent build  
sha-xxxxxxx → specific commit version  

This allows both:

Stable deployments  
Traceability  

---

# Conditional Push

Images are only pushed when:

branch == main

Feature branches:
Image builds but is NOT pushed

---

# End-to-End Flow

git push  
↓  
GitHub Actions triggered  
↓  
Docker image built  
↓  
Image tagged  
↓  
Image pushed to Docker Hub  
↓  
Image pulled and run anywhere  

---

# Running the Image

docker pull <username>/github-actions-practice:latest  
docker run <username>/github-actions-practice:latest  

---

# Key Learning

CI/CD pipelines can fully automate container workflows:

Build  
Tag  
Push  
Deploy  

This is the foundation of modern cloud-native applications.
