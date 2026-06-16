# 🚀 Kubernetes Level-1 | Task-06 | Rollback a Deployment

## 🎯 Objective

Rollback an existing Deployment to its **previous revision**.

| Parameter       | Value              |
| --------------- | ------------------ |
| Deployment Name | `nginx-deployment` |
| Action          | Rollback           |
| Target Revision | Previous Revision  |

---

## 🏗️ Architecture

### Before Rollback

```text
Deployment (nginx-deployment)
        │
        ▼
ReplicaSet (Revision 2)
        │
        ▼
Pods
        │
        ▼
nginx:1.17   ❌ Buggy Version
```

---

### After Rollback

```text
Deployment (nginx-deployment)
        │
        ▼
ReplicaSet (Revision 1)
        │
        ▼
Pods
        │
        ▼
nginx:1.16   ✅ Stable Version
```

---

# 📝 Solution

## Step 1: Check Rollout History

```bash
kubectl rollout history deployment nginx-deployment
```

Example:

```text
deployment.apps/nginx-deployment

REVISION   CHANGE-CAUSE
1          nginx:1.16
2          nginx:1.17
```

---

## Step 2: Rollback to Previous Revision

```bash
kubectl rollout undo deployment nginx-deployment
```

Expected Output:

```text
deployment.apps/nginx-deployment rolled back
```

---

## Step 3: Monitor Rollback Status

```bash
kubectl rollout status deployment nginx-deployment
```

Expected:

```text
deployment "nginx-deployment" successfully rolled out
```

---

## Step 4: Verify Deployment Image

```bash
kubectl describe deployment nginx-deployment
```

or

```bash
kubectl get deployment nginx-deployment -o yaml | grep image
```

Expected:

```text
image: nginx:1.16
```

---

## Step 5: Verify Pods

```bash
kubectl get pods
```

Expected:

```text
NAME                                READY   STATUS
nginx-deployment-xxxxx             1/1     Running
nginx-deployment-yyyyy             1/1     Running
nginx-deployment-zzzzz             1/1     Running
```

All Pods should be:

✅ Running

---

# ⚡ Fastest KodeKloud Solution

```bash
kubectl rollout undo deployment nginx-deployment

kubectl rollout status deployment nginx-deployment
```

---

# 📚 Key Concepts Learned

## 🔙 What is Rollback?

Rollback means reverting an application to a previous stable version.

Example:

```text
Revision 1 → nginx:1.16 ✅ Stable

Revision 2 → nginx:1.17 ❌ Buggy

Rollback

Revision 1 ← Active Again
```

---

## 🧠 How Kubernetes Supports Rollback

Kubernetes Deployments keep track of:

* ReplicaSets
* Deployment revisions
* Image history

Example:

```text
Deployment
    │
 ┌──┴────────────┐
 │               │
Revision 1    Revision 2
(1.16)        (1.17)
```

Rollback simply switches back to the old ReplicaSet.

---

## 📦 What Happens Internally?

When:

```bash
kubectl rollout undo deployment nginx-deployment
```

Kubernetes:

1. Finds the previous ReplicaSet.
2. Scales it up.
3. Gradually removes the current ReplicaSet.
4. Maintains application availability.

---

## 🔍 Check Rollout History

```bash
kubectl rollout history deployment nginx-deployment
```

Example:

```text
REVISION
1
2
3
```

---

## Rollback to Specific Revision

Rollback to revision 1:

```bash
kubectl rollout undo deployment nginx-deployment --to-revision=1
```

Very useful in production environments.

---

# 🛠️ Useful Commands

### View Rollout History

```bash
kubectl rollout history deployment nginx-deployment
```

### Rollback to Previous Revision

```bash
kubectl rollout undo deployment nginx-deployment
```

### Rollback to Specific Revision

```bash
kubectl rollout undo deployment nginx-deployment --to-revision=1
```

### Monitor Rollback

```bash
kubectl rollout status deployment nginx-deployment
```

### View Deployment

```bash
kubectl describe deployment nginx-deployment
```

---

# 🎤 Interview Explanation

> "In this task, I rolled back the Deployment `nginx-deployment` to its previous revision using the `kubectl rollout undo` command. Kubernetes maintains Deployment revision history using ReplicaSets. During rollback, Kubernetes scaled up the previous ReplicaSet and gradually replaced the Pods running the buggy version with Pods running the stable version, ensuring zero downtime throughout the process."

---

# 🔥 Interview Questions

### 1️⃣ What is Rollback in Kubernetes?

Rollback is the process of reverting a Deployment to a previous stable version.

---

### 2️⃣ Which Kubernetes resource supports Rollback?

✅ Deployment

Pods and ReplicaSets alone do not support rollback.

---

### 3️⃣ What command performs rollback?

```bash
kubectl rollout undo deployment nginx-deployment
```

---

### 4️⃣ How does Kubernetes know the previous version?

Kubernetes stores:

* Deployment revisions
* ReplicaSets
* Pod templates

This history allows rollback.

---

### 5️⃣ How do you view Deployment history?

```bash
kubectl rollout history deployment nginx-deployment
```

---

### 6️⃣ Can you rollback to a specific revision?

✅ Yes

```bash
kubectl rollout undo deployment nginx-deployment --to-revision=1
```

---

### 7️⃣ Does rollback cause downtime?

❌ No

Rollback is performed as a rolling update, ensuring application availability.

---

### 8️⃣ What Kubernetes object stores revision history?

✅ ReplicaSet

Every Deployment update creates a new ReplicaSet.

---

### 9️⃣ What happens to the current ReplicaSet after rollback?

The old ReplicaSet becomes active again, and the current ReplicaSet is scaled down.

---

### 🔟 Difference Between Rolling Update and Rollback?

| Rolling Update         | Rollback                      |
| ---------------------- | ----------------------------- |
| Moves to newer version | Returns to older version      |
| Creates new ReplicaSet | Activates previous ReplicaSet |
| Used for upgrades      | Used for recovery             |
| Forward movement       | Backward movement             |

---

# 📌 Task Summary (Interview Revision)

This task introduces **Deployment Recovery**, one of the most important production Kubernetes features.

### Key Learnings

✅ Deployment Revision History
✅ ReplicaSets
✅ Rollback Strategy
✅ Zero-Downtime Recovery
✅ Rollout Monitoring
✅ Recovery from Failed Releases

💡 **Most Important Interview Point:**
Kubernetes Deployments maintain revision history automatically through ReplicaSets. If a release fails, a rollback can restore the previous stable version with **zero downtime**, which is one of the major advantages of using Deployments in production. 🚀


## 🔍 Check All Deployments in Kubernetes

### 1️⃣ List all Deployments in the current namespace

```bash
kubectl get deployments
```

or the short form:

```bash
kubectl get deploy
```

Example output:

```text
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           15m
httpd-deployment   1/1     1            1           5m
```

---

### 2️⃣ Count the number of Deployments

```bash
kubectl get deployments --no-headers | wc -l
```

Example:

```text
2
```

This means **2 deployments** are present in the current namespace.

---

### 3️⃣ List Deployments in **all namespaces**

```bash
kubectl get deployments -A
```

or

```bash
kubectl get deploy --all-namespaces
```

Example:

```text
NAMESPACE     NAME               READY   AGE
default       nginx-deployment   3/3     20m
dev           web-deployment     1/1     10m
kube-system   coredns            2/2     1d
```

---

### 4️⃣ Count Deployments across all namespaces

```bash
kubectl get deployments -A --no-headers | wc -l
```

---

## 🎤 Interview Question

### Q: What is the difference between these commands?

```bash
kubectl get deploy
```

and

```bash
kubectl get deploy -A
```

**Answer:**

* `kubectl get deploy` → Shows deployments only in the **current namespace**.
* `kubectl get deploy -A` → Shows deployments in **all namespaces** (`-A` = `--all-namespaces`).

---

## 💡 Pro Tip for KodeKloud / Interviews

Before performing operations like:

```bash
kubectl rollout undo deployment nginx-deployment
```

Always check:

```bash
kubectl get deploy
kubectl get rs
kubectl get pods
```

This helps you understand the relationship:

```text
Deployment
    ↓
ReplicaSet
    ↓
Pods
```

This hierarchy is one of the **most frequently asked Kubernetes interview topics**. 🚀

