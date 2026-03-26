# Day 50 – Kubernetes Architecture and Cluster Setup

---

## 🚀 Overview

In this lab, I moved from running standalone Docker containers to understanding **Kubernetes orchestration**, setting up a local cluster, and exploring its internal architecture.

---

## ✅ Expected Output

* ✔️ Local Kubernetes cluster running
* ✔️ `day-50-k8s-setup.md` created
* ✔️ `kubectl get nodes` verified (screenshot attached separately)

---

# 🧠 Task 1: Kubernetes Story

## From Memory

* Kubernetes manages containers across multiple servers
* Docker alone cannot handle large-scale orchestration
* It automates deployment and scaling

---

## Verified Understanding

### Why Kubernetes was created

Docker handles:

* Container creation and execution

But lacks:

* Multi-node orchestration
* Self-healing
* Auto-scaling
* Load balancing

Kubernetes solves:

* Running containers across clusters
* Maintaining desired system state
* High availability and fault tolerance

---

### Who created Kubernetes?

* Created by Google
* Inspired by Google's internal system: **Borg**

---

### Meaning of Kubernetes

* Greek word meaning **“Helmsman” or “Pilot”**
* Symbolizes controlling and steering container systems

---

# 🏗️ Task 2: Kubernetes Architecture

## Control Plane (Master Node)

* **API Server**

  * Entry point for all cluster communication

* **etcd**

  * Stores cluster state as key-value data

* **Scheduler**

  * Assigns pods to nodes

* **Controller Manager**

  * Ensures desired state matches actual state

---

## Worker Node

* **kubelet**

  * Node agent communicating with API server

* **kube-proxy**

  * Handles networking and service routing

* **Container Runtime**

  * Runs containers (containerd)

---

## 🔄 Request Flow: `kubectl apply -f pod.yaml`

1. kubectl sends request to API Server
2. API Server validates and stores data in etcd
3. Controller Manager detects new desired state
4. Scheduler selects a node
5. kubelet pulls image and starts container
6. kube-proxy configures networking

---

## ⚠️ Failure Scenarios

### API Server Down

* Cluster becomes read-only
* No new deployments
* Existing workloads continue running

---

### Worker Node Down

* Node marked **NotReady**
* Pods terminated
* Scheduler reschedules pods elsewhere

---

# ⚙️ Task 3: Install kubectl

## Installation (Linux)

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

## Verification

```
kubectl version --client
```

---

# 🖥️ Task 4: Local Cluster Setup

## Selected Tool: kind

### Reason

* Lightweight
* Runs inside Docker
* Fast startup
* Ideal for DevOps workflows

---

## Commands Used

```
kind create cluster --name devops-cluster
kubectl cluster-info
kubectl get nodes
```

---

# 🔍 Task 5: Explore Cluster

## Commands Executed

```
kubectl cluster-info
kubectl get nodes
kubectl describe node <node-name>
kubectl get namespaces
kubectl get pods -A
kubectl get pods -n kube-system
```

---

## Observations

### kube-system Pods Mapping

| Pod                     | Component  |
| ----------------------- | ---------- |
| kube-apiserver          | API Server |
| etcd                    | Database   |
| kube-scheduler          | Scheduler  |
| kube-controller-manager | Controller |
| coredns                 | DNS        |
| kube-proxy              | Networking |

---

### Key Insight

Kubernetes runs its own core components as **pods inside the cluster**.

---

# 🔁 Task 6: Cluster Lifecycle

## Commands

```
kind delete cluster --name devops-cluster
kind create cluster --name devops-cluster
kubectl get nodes
```

---

## Context Management

```
kubectl config current-context
kubectl config get-contexts
kubectl config view
```

---

# 📄 kubeconfig

## What is kubeconfig?

A configuration file that:

* Stores cluster connection details
* Handles authentication
* Defines contexts

---

## Default Location

```
~/.kube/config
```

---

## Importance

* Prevents deploying to wrong cluster
* Enables multi-cluster management
* Essential in production environments

---

# 📸 Final Verification

Command:

```
kubectl get nodes
```

Expected Output:

* Node status: **Ready**

(Screenshot attached separately)

---

# 📌 Key Takeaways

* Kubernetes is **declarative**
* Works on **desired state reconciliation**
* Uses **control loops and event-driven architecture**
* Control Plane = decision-making
* Worker Nodes = execution

---

# 🧩 Conclusion

This lab established foundational Kubernetes knowledge:

* Architecture understanding
* Cluster setup
* Core command usage

This marks the transition from **containerization → orchestration**.

---
