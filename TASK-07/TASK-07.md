# 🚀 Kubernetes Level-1 | Task-07 | Create a ReplicaSet

## 🎯 Objective

Create a ReplicaSet with the following requirements:

| Parameter       | Value              |
| --------------- | ------------------ |
| ReplicaSet Name | `httpd-replicaset` |
| Image           | `httpd:latest`     |
| Container Name  | `httpd-container`  |
| Replicas        | `4`                |
| Label           | `app=httpd_app`    |
| Label           | `type=front-end`   |

---

## 🏗️ Architecture

```text
ReplicaSet (httpd-replicaset)
        │
        │ Maintains 4 Replicas
        ▼
 ┌────────────────────┐
 │ Pod-1              │
 │ httpd-container    │
 │ httpd:latest       │
 └────────────────────┘

 ┌────────────────────┐
 │ Pod-2              │
 │ httpd-container    │
 │ httpd:latest       │
 └────────────────────┘

 ┌────────────────────┐
 │ Pod-3              │
 │ httpd-container    │
 │ httpd:latest       │
 └────────────────────┘

 ┌────────────────────┐
 │ Pod-4              │
 │ httpd-container    │
 │ httpd:latest       │
 └────────────────────┘
```

---

# 📝 Solution

## rs.yaml

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: httpd-replicaset
  labels:
    app: httpd_app
    type: front-end

spec:
  replicas: 4

  selector:
    matchLabels:
      app: httpd_app
      type: front-end

  template:
    metadata:
      labels:
        app: httpd_app
        type: front-end

    spec:
      containers:
      - name: httpd-container
        image: httpd:latest
```

---

## 🚀 Deploy ReplicaSet

```bash
kubectl apply -f rs.yaml
```

Expected:

```text
replicaset.apps/httpd-replicaset created
```

---

# ✅ Verification Commands

### 1. Check ReplicaSet

```bash
kubectl get rs
```

Expected:

```text
NAME               DESIRED   CURRENT   READY
httpd-replicaset   4         4         4
```

---

### 2. Check ReplicaSet Labels ⭐

```bash
kubectl get rs --show-labels
```

Expected:

```text
NAME               DESIRED CURRENT READY LABELS
httpd-replicaset   4       4       4     app=httpd_app,type=front-end
```

---

### 3. Check Pods

```bash
kubectl get pods
```

Expected:

```text
NAME                         READY STATUS
httpd-replicaset-abcde       1/1   Running
httpd-replicaset-fghij       1/1   Running
httpd-replicaset-klmno       1/1   Running
httpd-replicaset-pqrst       1/1   Running
```

---

### 4. Check Pod Labels ⭐

```bash
kubectl get pods --show-labels
```

Expected:

```text
NAME                         LABELS
httpd-replicaset-abcde       app=httpd_app,type=front-end
httpd-replicaset-fghij       app=httpd_app,type=front-end
httpd-replicaset-klmno       app=httpd_app,type=front-end
httpd-replicaset-pqrst       app=httpd_app,type=front-end
```

---

### 5. Describe ReplicaSet

```bash
kubectl describe rs httpd-replicaset
```

This shows:

* Replica count
* Labels
* Selector
* Container Image
* Events
* Pod status

---

### 6. Export YAML

```bash
kubectl get rs httpd-replicaset -o yaml
```

---

# 📚 Key Concepts Learned

## 🔄 What is a ReplicaSet?

A ReplicaSet ensures that a specified number of identical Pods are always running.

Example:

```yaml
replicas: 4
```

Meaning:

```text
Desired Pods = 4
```

If:

```text
Current Pods = 3
```

ReplicaSet creates:

```text
1 New Pod
```

Automatically.

This is called:

✅ Self-Healing

---

## 🏷️ Labels and Selectors

The most important part of ReplicaSet:

```yaml
selector:
  matchLabels:
    app: httpd_app
    type: front-end
```

must match:

```yaml
template:
  metadata:
    labels:
      app: httpd_app
      type: front-end
```

---

## ⭐ KodeKloud Important Note

For many KodeKloud validations, it is safer to include labels here too:

```yaml
metadata:
  labels:
    app: httpd_app
    type: front-end
```

Although optional in Kubernetes, some validators check these labels.

---

## 🚦 Desired State vs Current State

ReplicaSet continuously checks:

```text
Desired State = 4 Pods

Current State = ?
```

If:

```text
Desired = 4
Current = 2
```

ReplicaSet creates:

```text
2 New Pods
```

This continuous monitoring is called:

### Reconciliation Loop

One of Kubernetes' core concepts.

---

## 🔥 Self-Healing Example

Delete a Pod:

```bash
kubectl delete pod <pod-name>
```

Immediately:

```text
ReplicaSet notices:

Desired = 4
Current = 3

Creates New Pod
```

This is:

✅ Self-Healing

---

## 🆚 ReplicaSet vs Deployment

| ReplicaSet           | Deployment               |
| -------------------- | ------------------------ |
| Maintains Pod count  | Manages ReplicaSets      |
| Self-Healing         | Self-Healing             |
| Scaling              | Scaling                  |
| No Rolling Updates   | Supports Rolling Updates |
| No Rollback          | Supports Rollback        |
| Rarely used directly | Most commonly used       |

---

## Architecture

```text
Deployment
     │
     ▼
ReplicaSet
     │
     ▼
Pods
```

Production applications typically use:

```text
Deployment → ReplicaSet → Pods
```

---

# 🎤 Interview Explanation

> "In this task, I created a ReplicaSet named `httpd-replicaset` that maintains four Pods running the `httpd:latest` image. I configured labels `app=httpd_app` and `type=front-end` in the ReplicaSet metadata, selector, and Pod template. ReplicaSet continuously compares the desired state with the current state and automatically recreates Pods if they fail, providing self-healing capabilities."

---

# 🔥 Interview Questions

### 1️⃣ What is a ReplicaSet?

A ReplicaSet ensures a specified number of Pod replicas are always running.

---

### 2️⃣ Why do we use ReplicaSets?

* High Availability
* Self-Healing
* Pod Replication
* Scaling

---

### 3️⃣ What happens if a Pod crashes?

ReplicaSet automatically creates a replacement Pod.

---

### 4️⃣ What is the role of Labels?

Labels identify Kubernetes resources.

Example:

```yaml
labels:
  app: httpd_app
```

---

### 5️⃣ What is the role of Selector?

Selector tells ReplicaSet:

> "Which Pods belong to me?"

Example:

```yaml
selector:
  matchLabels:
    app: httpd_app
```

---

### 6️⃣ What happens if Labels and Selector don't match?

ReplicaSet cannot manage Pods.

Example:

```yaml
selector:
  app: httpd_app

labels:
  app: http_app
```

❌ Pods are ignored.

This is one of the **most frequently asked Kubernetes interview questions**.

---

### 7️⃣ What is Self-Healing?

If a Pod fails:

```text
Desired = 4
Current = 3
```

ReplicaSet automatically creates:

```text
1 New Pod
```

---

### 8️⃣ What is the Reconciliation Loop?

Kubernetes continuously compares:

```text
Desired State
vs
Current State
```

and takes corrective actions automatically.

---

### 9️⃣ Can ReplicaSet perform Rolling Updates?

❌ No

Rolling Updates are handled by:

✅ Deployment

---

### 🔟 Why is Deployment preferred over ReplicaSet?

Because Deployment provides:

* ReplicaSets
* Rolling Updates
* Rollbacks
* Revision History

Therefore:

```text
Deployment → ReplicaSet → Pods
```

is the standard production architecture. 🚀

---

# 📌 Task Summary (Interview Revision)

This task introduces the **self-healing engine of Kubernetes**.

### Key Learnings

✅ ReplicaSet Creation
✅ Labels and Selectors
✅ `--show-labels` Verification
✅ Desired vs Current State
✅ Reconciliation Loop
✅ Self-Healing
✅ Scaling Pods
✅ ReplicaSet vs Deployment

💡 **Most Important Interview Point:**
A ReplicaSet continuously watches the cluster and ensures the **desired number of Pods are always running**. This automated reconciliation mechanism is one of the foundational principles of Kubernetes architecture. 🚀
