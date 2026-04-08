# Day 60 – Capstone: WordPress + MySQL on Kubernetes

> Ten days. Twelve concepts. One production-grade deployment.

---

## Overview

This capstone deploys a complete **WordPress + MySQL** stack on Kubernetes, combining every major concept from Days 51–59 into a single, coherent application. All resources live in the `capstone` namespace and are applied from the `manifests/` directory.

### Concepts Used

| # | Concept | Resource |
|---|---------|----------|
| 1 | Namespace | `capstone` |
| 2 | Secret | `mysql-secret` |
| 3 | ConfigMap | `wordpress-config` |
| 4 | StatefulSet | `mysql` |
| 5 | Headless Service | `mysql` (clusterIP: None) |
| 6 | PersistentVolumeClaim | `mysql-data-mysql-0` (via volumeClaimTemplate) |
| 7 | Deployment | `wordpress` |
| 8 | NodePort Service | `wordpress` (port 30080) |
| 9 | Resource Requests & Limits | Both MySQL and WordPress |
| 10 | Liveness & Readiness Probes | Both MySQL and WordPress |
| 11 | HorizontalPodAutoscaler | `wordpress-hpa` |
| 12 | Helm (Bonus) | `bitnami/wordpress` in `wp-helm` namespace |

---

## Architecture

```
                        ┌─────────────────────────────────────────┐
                        │           capstone namespace            │
                        │                                         │
  Browser               │  ┌──────────────┐   ┌──────────────┐  │
  :30080  ─────────────►│  │  wordpress   │   │  wordpress   │  │
                        │  │   pod-0      │   │   pod-1      │  │
                        │  └──────┬───────┘   └──────┬───────┘  │
                        │         │   Deployment (2-10 replicas) │
                        │         │       ▲ HPA (50% CPU)        │
                        │  NodePort│       │                      │
                        │  Service │  ┌────┴──────────────────┐  │
                        │  :30080  │  │    wordpress-config   │  │
                        │          │  │    ConfigMap          │  │
                        │          │  │  + mysql-secret       │  │
                        │          │  └───────────────────────┘  │
                        │          │                              │
                        │          │  mysql-0.mysql.capstone      │
                        │          │  .svc.cluster.local:3306     │
                        │          ▼                              │
                        │  ┌──────────────────────────────────┐  │
                        │  │  Headless Service: mysql          │  │
                        │  │  (clusterIP: None)               │  │
                        │  └──────────────┬───────────────────┘  │
                        │                 │                       │
                        │  ┌──────────────▼───────────────────┐  │
                        │  │  StatefulSet: mysql-0            │  │
                        │  │  image: mysql:8.0                │  │
                        │  │  cpu: 250m–500m  mem: 512Mi–1Gi  │  │
                        │  └──────────────┬───────────────────┘  │
                        │                 │ volumeClaimTemplate   │
                        │  ┌──────────────▼───────────────────┐  │
                        │  │  PVC: mysql-data-mysql-0 (1Gi)   │  │
                        │  └──────────────────────────────────┘  │
                        └─────────────────────────────────────────┘
```

---

## File Structure

```
capstone/
├── manifests/
│   ├── kustomization.yaml            # Apply all at once
│   ├── 01-namespace.yaml
│   ├── 02-mysql-secret.yaml
│   ├── 03-mysql-headless-service.yaml
│   ├── 04-mysql-statefulset.yaml
│   ├── 05-wordpress-configmap.yaml
│   ├── 06-wordpress-deployment.yaml
│   ├── 07-wordpress-service.yaml
│   └── 08-wordpress-hpa.yaml
└── day-60-capstone.md                # ← this file
```

---

## Task 1: Create the Namespace

```yaml
# 01-namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: capstone
  labels:
    project: wordpress-capstone
    day: "60"
```

```bash
kubectl apply -f manifests/01-namespace.yaml
kubectl config set-context --current --namespace=capstone
```

**Why:** Every production workload deserves its own namespace for isolation, RBAC scoping, and clean teardown (`kubectl delete namespace capstone` removes everything at once).

---

## Task 2: Deploy MySQL

### Secret — `02-mysql-secret.yaml`

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
  namespace: capstone
type: Opaque
stringData:
  MYSQL_ROOT_PASSWORD: "r00tP@ssw0rd!"
  MYSQL_DATABASE: "wordpress"
  MYSQL_USER: "wpuser"
  MYSQL_PASSWORD: "wpP@ssw0rd!"
```

`stringData` lets you write plain text — Kubernetes base64-encodes it automatically. Never commit real passwords to Git; use Sealed Secrets or Vault in production.

### Headless Service — `03-mysql-headless-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
  namespace: capstone
spec:
  clusterIP: None     # ← Headless!
  selector:
    app: mysql
  ports:
    - port: 3306
```

`clusterIP: None` means DNS returns the Pod IP directly (`mysql-0.mysql.capstone.svc.cluster.local`), not a virtual IP. Essential for StatefulSets so each pod has a stable, addressable identity.

### StatefulSet — `04-mysql-statefulset.yaml`

Key sections explained:

```yaml
spec:
  serviceName: "mysql"        # Links to the Headless Service
  replicas: 1
  template:
    spec:
      containers:
        - name: mysql
          image: mysql:8.0
          envFrom:
            - secretRef:
                name: mysql-secret   # All 4 vars injected at once
          resources:
            requests: { cpu: 250m, memory: 512Mi }
            limits:   { cpu: 500m, memory: 1Gi }
  volumeClaimTemplates:        # PVC created per pod, persists across restarts
    - metadata:
        name: mysql-data
      spec:
        accessModes: [ReadWriteOnce]
        resources:
          requests:
            storage: 1Gi
```

**Why StatefulSet over Deployment?** StatefulSets give each pod a stable network identity (`mysql-0`) and its own PVC that survives pod deletion. Deployments would give you a random pod name and shared (or no) storage.

```bash
kubectl apply -f manifests/02-mysql-secret.yaml
kubectl apply -f manifests/03-mysql-headless-service.yaml
kubectl apply -f manifests/04-mysql-statefulset.yaml

# Wait for mysql-0 to be Running
kubectl wait pod/mysql-0 -n capstone --for=condition=Ready --timeout=120s

# Verify
kubectl exec -it mysql-0 -n capstone -- \
  mysql -u wpuser -pwpP@ssw0rd! -e "SHOW DATABASES;"
```

**Expected output:**
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| wordpress          |    ← ✅ Your database is ready
+--------------------+
```

---

## Task 3: Deploy WordPress

### ConfigMap — `05-wordpress-configmap.yaml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: wordpress-config
  namespace: capstone
data:
  WORDPRESS_DB_HOST: "mysql-0.mysql.capstone.svc.cluster.local:3306"
  WORDPRESS_DB_NAME: "wordpress"
```

The DNS name `mysql-0.mysql.capstone.svc.cluster.local` is the stable address provided by the Headless Service + StatefulSet. Even if the pod restarts, it comes back as `mysql-0` with the same DNS name.

### Deployment — `06-wordpress-deployment.yaml`

```yaml
spec:
  replicas: 2
  template:
    spec:
      containers:
        - name: wordpress
          image: wordpress:latest
          envFrom:
            - configMapRef:
                name: wordpress-config    # DB host + DB name
          env:
            - name: WORDPRESS_DB_USER
              valueFrom:
                secretKeyRef:             # secretKeyRef for sensitive values
                  name: mysql-secret
                  key: MYSQL_USER
            - name: WORDPRESS_DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_PASSWORD
          resources:
            requests: { cpu: 250m, memory: 256Mi }
            limits:   { cpu: 500m, memory: 512Mi }
          readinessProbe:
            httpGet: { path: /wp-login.php, port: 80 }
            initialDelaySeconds: 30
            periodSeconds: 10
          livenessProbe:
            httpGet: { path: /wp-login.php, port: 80 }
            initialDelaySeconds: 60
            periodSeconds: 20
```

**envFrom vs secretKeyRef:** Non-sensitive config (DB host, DB name) comes from the ConfigMap via `envFrom`. Sensitive credentials (user, password) are cherry-picked from the Secret via `secretKeyRef` — this way they're never accidentally exposed in a ConfigMap dump.

**Probes:** The readiness probe prevents traffic from reaching a pod until WordPress is fully booted. The liveness probe restarts the pod if it becomes unhealthy (e.g., stuck PHP-FPM process).

```bash
kubectl apply -f manifests/05-wordpress-configmap.yaml
kubectl apply -f manifests/06-wordpress-deployment.yaml

# Watch both pods come up
kubectl get pods -n capstone -l app=wordpress -w
```

**Expected:**
```
NAME                         READY   STATUS    RESTARTS
wordpress-7d9f8b6c4-xk2pq   1/1     Running   0
wordpress-7d9f8b6c4-mn8qr   1/1     Running   0
```

---

## Task 4: Expose WordPress

### NodePort Service — `07-wordpress-service.yaml`

```yaml
spec:
  type: NodePort
  selector:
    app: wordpress
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

```bash
kubectl apply -f manifests/07-wordpress-service.yaml

# Minikube users:
minikube service wordpress -n capstone

# Kind / kubeadm users:
kubectl port-forward svc/wordpress 8080:80 -n capstone
```

Open your browser to the URL printed above → you should see the WordPress 5-minute setup wizard. Complete it, then write a test blog post titled **"Day 60 – I deployed Kubernetes!"**

---

## Task 5: Test Self-Healing and Persistence

### Test 1 — WordPress pod self-healing

```bash
# List current pods
kubectl get pods -n capstone -l app=wordpress

# Delete one pod
kubectl delete pod <wordpress-pod-name> -n capstone

# Watch the Deployment immediately recreate it
kubectl get pods -n capstone -l app=wordpress -w
```

**Expected:** Within ~5 seconds, a new pod appears and reaches `1/1 Running`. The site stays available because the second pod handles traffic during the restart (readiness probe ensures no broken pod receives traffic).

### Test 2 — MySQL pod self-healing + data persistence

```bash
# Delete the MySQL pod
kubectl delete pod mysql-0 -n capstone

# Watch the StatefulSet recreate it (same name, same PVC!)
kubectl get pods -n capstone -l app=mysql -w
```

**Expected:** `mysql-0` reappears within ~30 seconds. Once Ready, refresh WordPress in your browser — your blog post is still there because the PVC (`mysql-data-mysql-0`) was **never deleted**; the StatefulSet simply re-attached it to the new pod.

```bash
# Confirm data survived
kubectl exec -it mysql-0 -n capstone -- \
  mysql -u wpuser -pwpP@ssw0rd! wordpress \
  -e "SELECT post_title FROM wp_posts WHERE post_status='publish';"
```

**Expected:**
```
+----------------------------------+
| post_title                       |
+----------------------------------+
| Day 60 – I deployed Kubernetes!  |   ← ✅ Data survived!
+----------------------------------+
```

---

## Task 6: Set Up HPA

### HPA Manifest — `08-wordpress-hpa.yaml`

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: wordpress-hpa
  namespace: capstone
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: wordpress
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
  behavior:
    scaleUp:
      stabilizationWindowSeconds: 30     # Fast scale-up
    scaleDown:
      stabilizationWindowSeconds: 300    # Slow scale-down (avoid flapping)
```

```bash
kubectl apply -f manifests/08-wordpress-hpa.yaml

# Verify HPA
kubectl get hpa -n capstone
```

**Expected:**
```
NAME            REFERENCE              TARGETS   MINPODS   MAXPODS   REPLICAS
wordpress-hpa   Deployment/wordpress   8%/50%    2         10        2
```

The `TARGETS` column shows current CPU% / threshold%. At 2 replicas with minimal load, it sits well below 50% and holds steady.

### Full Stack View

```bash
kubectl get all -n capstone
```

**Expected output:**
```
NAME                             READY   STATUS    RESTARTS   AGE
pod/mysql-0                      1/1     Running   0          15m
pod/wordpress-7d9f8b6c4-mn8qr   1/1     Running   0          12m
pod/wordpress-7d9f8b6c4-xk2pq   1/1     Running   0          12m

NAME                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/mysql       ClusterIP   None            <none>        3306/TCP       15m
service/wordpress   NodePort    10.96.142.201   <none>        80:30080/TCP   10m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/wordpress   2/2     2            2           12m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/wordpress-7d9f8b6c4   2         2         2       12m

NAME                                    READY   AGE
statefulset.apps/mysql                  1/1     15m

NAME                                            REFERENCE              TARGETS   MINPODS   MAXPODS   REPLICAS
horizontalpodautoscaler.apps/wordpress-hpa      Deployment/wordpress   8%/50%    2         10        2
```

---

## Task 7 (Bonus): Compare with Helm

```bash
# Add Bitnami repo
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Install in a separate namespace
helm install wp-helm bitnami/wordpress \
  --namespace wp-helm \
  --create-namespace \
  --set wordpressUsername=admin \
  --set wordpressPassword=adminPass123

# See what Helm created
kubectl get all -n wp-helm | wc -l
```

### Comparison

| Aspect | Manual (12 files) | Helm (`bitnami/wordpress`) |
|--------|-------------------|----------------------------|
| Resources created | ~8 manifests, you control every line | 20+ resources auto-generated |
| Control | Total — you see and own every field | Partial — override via `values.yaml` |
| Repeatability | Script or Kustomize required | `helm install` is one command |
| Upgrades | Manual `kubectl apply` per file | `helm upgrade` with rollback |
| Learning value | ⭐⭐⭐⭐⭐ — understand every concept | ⭐⭐ — abstracted away |
| Production speed | Slow first time, fast with templates | Fast from day one |

**Verdict:** Helm is faster for getting something running, but you pay with opacity. Understanding the manual approach (this capstone) is what makes you dangerous with Helm — you know what it's doing under the hood.

```bash
# Clean up Helm deployment
helm uninstall wp-helm -n wp-helm
kubectl delete namespace wp-helm
```

---

## Task 8: Clean Up and Reflect

```bash
# Final view before teardown
kubectl get all -n capstone
kubectl get pvc -n capstone
kubectl get secrets -n capstone
kubectl get configmaps -n capstone

# Delete EVERYTHING in one command
kubectl delete namespace capstone

# Reset default namespace
kubectl config set-context --current --namespace=default

# Verify it's gone
kubectl get all -n capstone   # Error: namespace not found ✅
```

Deleting the namespace cascades to every resource inside it: Pods, Services, Deployments, StatefulSets, HPAs, ConfigMaps, Secrets — **and PVCs**. The underlying PersistentVolumes may remain depending on your storage class `reclaimPolicy` (`Delete` vs `Retain`).

---

## Key Lessons Learned

### 1. StatefulSet vs Deployment — When to use which
- **StatefulSet:** Databases, message brokers, anything needing stable identity + dedicated storage
- **Deployment:** Stateless web apps, APIs — anything where pods are interchangeable

### 2. envFrom vs secretKeyRef — Mixing both intentionally
Using `envFrom` for the ConfigMap (all vars) and `secretKeyRef` for individual Secret keys is a deliberate security pattern: ConfigMaps can be read by anyone with namespace access, while Secrets (ideally) have tighter RBAC. Picking individual keys from a Secret also avoids leaking unrelated credentials.

### 3. Probes — Timing matters
Setting `initialDelaySeconds` too low causes crash loops. WordPress takes ~30s to connect to MySQL and initialize. The liveness probe fires later (60s) than the readiness probe (30s) so a slow-starting pod isn't killed before it has a chance to become ready.

### 4. Headless Services — The StatefulSet DNS trick
The DNS name `pod-name.service-name.namespace.svc.cluster.local` only works because of the Headless Service (`clusterIP: None`). Without it, you'd have to use a ClusterIP VIP which load-balances across all pods — not what you want for a primary database.

### 5. HPA stabilization windows — Preventing flapping
Setting a short scale-up window (30s) and long scale-down window (300s) is a classic pattern. Traffic spikes are real; rapid scale-up protects the site. Brief lulls between spikes shouldn't trigger scale-in — the 5-minute window absorbs that noise.

### 6. Namespace as blast radius control
`kubectl delete namespace capstone` cleanly removed 12 resources across 8 resource types in one command. This is the operational payoff of namespace-scoping everything.

---

## Concepts Mastered: Days 51–60

```
Day 51 – Introduction & Architecture
Day 52 – Namespaces & kubectl         → capstone namespace
Day 53 – Services                     → NodePort + Headless
Day 54 – ConfigMaps & Secrets         → wordpress-config, mysql-secret
Day 55 – Storage & PVCs               → volumeClaimTemplates
Day 56 – StatefulSets                 → mysql StatefulSet
Day 57 – Deployments & Probes         → wordpress Deployment + liveness/readiness
Day 58 – Resource Limits & HPA        → requests/limits + HPA
Day 59 – Helm                         → bitnami/wordpress comparison
Day 60 – Capstone ✅                  → Everything, together
```

---

*Day 60 complete. From a blank cluster to a self-healing, auto-scaling, persistent WordPress site — one namespace, twelve Kubernetes concepts.*
