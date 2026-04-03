# Day 54 – Kubernetes ConfigMaps and Secrets

---

## 🚀 Overview

Today I tackled **configuration management in Kubernetes**.

Hardcoding configs inside images is a bad practice.
Kubernetes solves this with:

* **ConfigMaps** → non-sensitive configuration
* **Secrets** → sensitive data (credentials, passwords)

---



# 🧠 Task 1: ConfigMap from Literals

## Create ConfigMap

```
kubectl create configmap app-config \
  --from-literal=APP_ENV=production \
  --from-literal=APP_DEBUG=false \
  --from-literal=APP_PORT=8080
```

---

## Inspect

```
kubectl describe configmap app-config
kubectl get configmap app-config -o yaml
```

---

## Verification

✔️ All key-value pairs visible
✔️ Stored as **plain text (not encrypted)**

---

# 🧩 Task 2: ConfigMap from File

## Custom Nginx Config (`default.conf`)

```
server {
    listen 80;

    location /health {
        return 200 'healthy';
    }
}
```

---

## Create ConfigMap

```
kubectl create configmap nginx-config --from-file=default.conf=default.conf
```

---

## Verify

```
kubectl get configmap nginx-config -o yaml
```

✔️ File content stored correctly

---

# ⚙️ Task 3: Use ConfigMaps in Pods

## Pod 1: Environment Variables

```
apiVersion: v1
kind: Pod
metadata:
  name: configmap-env-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "env && sleep 3600"]
    envFrom:
    - configMapRef:
        name: app-config
```

---

## Pod 2: Volume Mount (Nginx)

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-config-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    volumeMounts:
    - name: config-volume
      mountPath: /etc/nginx/conf.d
  volumes:
  - name: config-volume
    configMap:
      name: nginx-config
```

---

## Test

```
kubectl exec nginx-config-pod -- curl -s http://localhost/health
```

---

## Verification

✔️ `/health` endpoint returns **healthy**

---

## Key Insight

* env → simple key-values
* volume → full config files

---

# 🔐 Task 4: Create a Secret

## Create Secret

```
kubectl create secret generic db-credentials \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASSWORD=s3cureP@ssw0rd
```

---

## Inspect

```
kubectl get secret db-credentials -o yaml
```

---

## Decode

```
echo '<base64-value>' | base64 --decode
```

---

## Verification

✔️ Values decoded successfully

---

## Important Insight

* Base64 ≠ Encryption
* Security comes from:

  * RBAC
  * Access control
  * Encryption at rest (optional)

---

# 🔑 Task 5: Use Secrets in Pod

## Manifest

```
apiVersion: v1
kind: Pod
metadata:
  name: secret-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "env && sleep 3600"]
    env:
    - name: DB_USER
      valueFrom:
        secretKeyRef:
          name: db-credentials
          key: DB_USER
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/db-credentials
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: db-credentials
```

---

## Verification

✔️ Env variable injected
✔️ Secret mounted as files

---

## Key Insight

* Mounted files contain **plaintext values**
* Not base64 encoded

---

# 🔄 Task 6: Live Config Updates

## Create ConfigMap

```
kubectl create configmap live-config --from-literal=message=hello
```

---

## Pod Manifest

```
apiVersion: v1
kind: Pod
metadata:
  name: live-config-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "while true; do cat /config/message; sleep 5; done"]
    volumeMounts:
    - name: config-volume
      mountPath: /config
  volumes:
  - name: config-volume
    configMap:
      name: live-config
```

---

## Update ConfigMap

```
kubectl patch configmap live-config \
  --type merge \
  -p '{"data":{"message":"world"}}'
```

---

## Verification

✔️ File updated automatically (after ~30s)
✔️ No pod restart needed

---

## Critical Insight

* Volume mounts → dynamic updates
* Env variables → static (require restart)

---

# 🧹 Task 7: Cleanup

```
kubectl delete pod configmap-env-pod
kubectl delete pod nginx-config-pod
kubectl delete pod secret-pod
kubectl delete pod live-config-pod

kubectl delete configmap app-config nginx-config live-config
kubectl delete secret db-credentials
```

---

# 📌 Key Takeaways

* ConfigMaps separate configuration from images
* Secrets handle sensitive data securely (relatively)
* Volume mounts enable dynamic updates
* Environment variables are static after startup
* Base64 encoding is NOT security

---

# 🧩 Conclusion

Today I learned how Kubernetes manages configuration:

Hardcoded configs ❌
Dynamic, externalized configs ✅

This is critical for building **scalable, secure, and maintainable systems**.

---
