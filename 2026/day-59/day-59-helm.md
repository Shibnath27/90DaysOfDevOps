# Day 59 – Helm: Kubernetes Package Manager

---

## 🚀 Overview

After manually creating multiple Kubernetes resources (Pods, Deployments, Services, ConfigMaps, Secrets, PVCs), today I streamlined everything using **Helm**.

👉 Helm = **package manager for Kubernetes**
👉 Think: `apt` for Ubuntu, but for Kubernetes applications

---



# 🧠 Task 1: Install Helm

## Installation
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
chmod 700 get_helm.sh
./get_helm.sh
```
## Verify Installation

```
helm version
helm env
```

---

## Core Concepts

* **Chart** → Collection of Kubernetes manifests (templates)
* **Release** → Running instance of a chart
* **Repository** → Collection of charts

---

## Result

✔️ Helm installed and ready

---

# 📦 Task 2: Add Repository & Search

```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

---

## Search Charts

```
helm search repo nginx
helm search repo bitnami
```

---

## Result

✔️ Bitnami repo contains **hundreds of production-ready charts**

---

# ⚙️ Task 3: Install a Chart

```
helm install my-nginx bitnami/nginx
```

---

## Verify

```
kubectl get all
helm list
helm status my-nginx
helm get manifest my-nginx
```

---

## Insight

👉 One Helm command created:

* Deployment
* Service
* Supporting resources

---

## Result

✔️ Nginx running via Helm release

---

# 🎛️ Task 4: Customize with Values

## View Default Values

```
helm show values bitnami/nginx
```

---

## Override via CLI

```
helm install nginx-custom bitnami/nginx \
  --set replicaCount=3 \
  --set service.type=NodePort
```

---

## Using Values File

### custom-values.yaml

```
replicaCount: 3

service:
  type: NodePort

resources:
  limits:
    cpu: 200m
    memory: 256Mi
```

---

## Install with Values File

```
helm install nginx-values bitnami/nginx -f custom-values.yaml
```

---

## Verify

```
helm get values nginx-values
kubectl get pods
kubectl get svc
```

---

## Result

✔️ Custom replicas and service type applied

---

# 🔁 Task 5: Upgrade & Rollback

## Upgrade

```
helm upgrade my-nginx bitnami/nginx --set replicaCount=5
```

---

## History

```
helm history my-nginx
```

---

## Rollback

```
helm rollback my-nginx 1
```

---

## Result

✔️ Rollback created new revision

---

## Insight

👉 Helm maintains **full release history**
👉 Similar to Deployment rollouts, but broader scope

---

# 🏗️ Task 6: Create Your Own Chart

## Scaffold

```
helm create my-app
```

---

## Key Files

* `Chart.yaml` → metadata
* `values.yaml` → configuration
* `templates/` → Kubernetes manifests

---

## Modify Values

```
replicaCount: 3

image:
  repository: nginx
  tag: 1.25
```

---

## Validate & Preview

```
helm lint my-app
helm template my-release ./my-app
```

---

## Install

```
helm install my-release ./my-app
```

---

## Upgrade

```
helm upgrade my-release ./my-app --set replicaCount=5
```

---

## Result

✔️ Custom chart deployed and scaled

---

# 🧹 Task 7: Clean Up

```
helm uninstall my-nginx
helm uninstall nginx-custom
helm uninstall nginx-values
helm uninstall my-release
```

---

## Verify

```
helm list
```

---

## Result

✔️ No active releases

---

# 📌 Key Takeaways

* Helm simplifies complex Kubernetes deployments
* Charts = reusable infrastructure templates
* Values = dynamic configuration
* Upgrade/Rollback = version control for infrastructure

---

## 🧠 Big Insight

Without Helm:
👉 Dozens of YAML files

With Helm:
👉 One command deploys everything

---

# 🧩 Conclusion

Today I moved from:

❌ Manual YAML management
➡️
✅ **Packaged, reusable, version-controlled deployments**

👉 This is how real-world Kubernetes applications are managed at scale.

