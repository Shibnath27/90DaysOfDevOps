# Day 55 – Persistent Volumes (PV) and Persistent Volume Claims (PVC)

---

## 🚀 Overview

Today I solved one of the most critical problems in Kubernetes:

👉 **Containers are ephemeral — storage is not**

To handle persistent data, Kubernetes provides:

* **PersistentVolume (PV)** → actual storage resource
* **PersistentVolumeClaim (PVC)** → request for storage

---



# 🧠 Task 1: Ephemeral Storage Problem

## Pod with emptyDir

```
apiVersion: v1
kind: Pod
metadata:
  name: ephemeral-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "date >> /data/message.txt && sleep 3600"]
    volumeMounts:
    - name: temp-storage
      mountPath: /data
  volumes:
  - name: temp-storage
    emptyDir: {}
```

---

## Test

```
kubectl exec ephemeral-pod -- cat /data/message.txt
```

---

## Result

✔️ File exists

---

## After Deletion & Recreation

```
kubectl delete pod ephemeral-pod
kubectl apply -f ephemeral-pod.yaml
```

✔️ Old data is gone
✔️ New timestamp created

👉 **Data is NOT persistent**

---

# 🧩 Task 2: Create PersistentVolume

## PV Manifest

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: manual-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /tmp/k8s-pv-data
```

---

## Apply

```
kubectl apply -f pv.yaml
kubectl get pv
```

---

## Verification

✔️ STATUS: **Available**

---

# 🧩 Task 3: Create PersistentVolumeClaim

## PVC Manifest

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: manual-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

---

## Apply

```
kubectl apply -f pvc.yaml
kubectl get pvc
kubectl get pv
```

---

## Verification

✔️ PVC STATUS: **Bound**
✔️ PV STATUS: **Bound**

👉 PVC successfully matched to PV

---

# 🧩 Task 4: Use PVC in Pod

## Pod Manifest

```
apiVersion: v1
kind: Pod
metadata:
  name: persistent-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "date >> /data/message.txt && sleep 3600"]
    volumeMounts:
    - name: persistent-storage
      mountPath: /data
  volumes:
  - name: persistent-storage
    persistentVolumeClaim:
      claimName: manual-pvc
```

---

## Test

```
kubectl exec persistent-pod -- cat /data/message.txt
```

---

## Delete & Recreate

```
kubectl delete pod persistent-pod
kubectl apply -f persistent-pod.yaml
kubectl exec persistent-pod -- cat /data/message.txt
```

---

## Verification

✔️ File contains **multiple timestamps**

👉 **Data persisted across Pod recreation**

---

# 🧠 Task 5: StorageClasses

## Commands

```
kubectl get storageclass
kubectl describe storageclass
```

---

## Observation

* Found default StorageClass
* Includes:

  * Provisioner
  * Reclaim policy
  * Volume binding mode

---

## Insight

👉 With StorageClasses:

* You create PVC
* Kubernetes automatically provisions PV

---

# ⚙️ Task 6: Dynamic Provisioning

## PVC Manifest (Dynamic)

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: dynamic-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: standard
  resources:
    requests:
      storage: 500Mi
```

---

## Apply

```
kubectl apply -f dynamic-pvc.yaml
kubectl get pvc
kubectl get pv
```

---

## Verification

✔️ New PV created automatically
✔️ PVC bound successfully

---

## Insight

* manual-pv → static provisioning
* dynamic-pvc → dynamic provisioning

---

# 🧹 Task 7: Cleanup

```
kubectl delete pod persistent-pod ephemeral-pod
kubectl delete pvc manual-pvc dynamic-pvc
kubectl get pv
```

---

## Observation

* Dynamic PV → deleted automatically
* Manual PV → STATUS = Released

---

## Final Cleanup

```
kubectl delete pv manual-pv
```

---

# 📌 Key Takeaways

* Containers are ephemeral → storage must not be
* PV = actual storage
* PVC = request for storage
* emptyDir = temporary
* StorageClasses enable automation
* Reclaim policies matter:

  * Retain → manual cleanup
  * Delete → auto cleanup

---

# 🧩 Conclusion

Today I learned how Kubernetes handles **stateful workloads**:

Stateless → Pods
Stateful → PV + PVC

This is the foundation for running:

* Databases
* Logs
* Persistent applications

---
