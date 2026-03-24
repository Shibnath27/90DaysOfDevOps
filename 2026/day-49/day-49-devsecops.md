# 🔒 Day 49 – DevSecOps: Securing My CI/CD Pipeline

---

## 📌 What is DevSecOps? 

DevSecOps means integrating security directly into the CI/CD pipeline so that vulnerabilities are detected early during development instead of after deployment. Instead of treating security as a separate phase, it becomes part of the automation.

This helps reduce risk, improve reliability, and fix issues faster.

---

## 🛡️ Security Enhancements Implemented

---

### 🔍 1. Docker Image Vulnerability Scanning (Trivy)

I added a Trivy scan in my main pipeline after the Docker image is built and pushed.

```yaml
- name: Scan Docker Image for Vulnerabilities
  uses: aquasecurity/trivy-action@0.35.0
  with:
    image-ref: ${{ needs.docker.outputs.image_url }}
    format: table
    severity: CRITICAL,HIGH
    exit-code: 1
```

### ✅ What it does:

* Scans the exact Docker image (`sha-*` tagged)
* Detects known CVEs (Common Vulnerabilities and Exposures)
* Fails the pipeline if HIGH or CRITICAL issues are found

---

### 📸 Screenshot

<img width="1366" height="615" alt="Screenshot 2026-03-24 232242" src="https://github.com/user-attachments/assets/c2794097-2f60-495e-9ab9-c270e7b58944" />


---

## 📦 2. Dependency Vulnerability Scan (PR Pipeline)

Added dependency scanning in the PR pipeline:

```yaml
- name: Check Dependencies for Vulnerabilities
  uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: critical
```

### ✅ What it does:

* Scans newly added dependencies in pull requests
* Prevents vulnerable packages from being merged

---

## 🔐 3. GitHub Secret Scanning

Enabled from repository settings:

* ✅ Secret scanning
* ✅ Push protection

### 🧠 What I Learned:

| Feature         | Behavior                   |
| --------------- | -------------------------- |
| Secret scanning | Detects secrets after push |
| Push protection | Blocks commits before push |

### ⚠️ Example:

If an AWS access key is committed:

* GitHub blocks the push (push protection)
* Or raises a security alert (secret scanning)

---

## 🔒 4. Workflow Permission Hardening

Applied least-privilege permissions across workflows:

### Default:

```yaml
permissions:
  contents: read
```

### PR workflows:

```yaml
permissions:
  contents: read
  pull-requests: write
```

### 🧠 Why this matters:

If a GitHub Action is compromised:

* Limited permissions reduce potential damage
* Prevent unauthorized repository changes

---

## 🔄 Updated Secure Pipeline

---

### 🔹 Pull Request Flow

```
PR Opened
 → Build & Test
 → Dependency Vulnerability Scan
 → PR Pass / Fail
```

---

### 🔹 Main Branch Flow

```
Merge to Main
 → Build & Test
 → Docker Build & Push
 → Trivy Image Scan (fail on HIGH/CRITICAL)
 → Deploy
```

---

### 🔹 Always Active

```
→ GitHub Secret Scanning
→ Push Protection
```

---

## ⚠️ Challenges Faced

* Understanding correct placement of security checks
* Avoiding incorrect Docker tag usage during scanning
* Ensuring the scanned image matches the deployed artifact
* Debugging YAML and pipeline integration issues

---

## 🚀 Key Learnings

* Security must be automated in CI/CD pipelines
* Always scan the **exact deployed artifact** (use SHA tags)
* Dependency vulnerabilities should be caught at PR stage
* Secrets must never be committed to source code
* Least-privilege access reduces security risks

---

## 🔥 Optional Enhancements (Future Scope)

* Upload Trivy results to GitHub Security tab (SARIF)
* Pin GitHub Actions to commit SHAs for supply chain security
* Add SAST tools like CodeQL or Bandit
* Use OIDC for secure cloud authentication (no long-lived secrets)

---

## 📌 Conclusion

This project evolved from a basic CI/CD pipeline into a **DevSecOps pipeline**.

Security is now enforced at multiple stages:

* Before merge (dependency scan)
* Before deployment (image scan)
* At repository level (secret scanning)

This ensures vulnerabilities are caught early and do not reach production.

---
