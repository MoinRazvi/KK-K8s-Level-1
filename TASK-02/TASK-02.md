# 🚀 Kubernetes Level-1 | Task-02 | Create a Deployment

## 🎯 Objective

Create a Kubernetes Deployment with the following specifications:

| Parameter       | Value          |
| --------------- | -------------- |
| Deployment Name | `nginx`        |
| Image           | `nginx:latest` |
| Replicas        | Default (1)    |

---

## 🏗️ Architecture

```text
Deployment (nginx)
        │
        ▼
   ReplicaSet
        │
        ▼
      Pod
        │
        ▼
Container (nginx:latest)
```

---

## 📝 Solution

### Method 1: Using kubectl Command

```bash
kubectl create deployment nginx --image=nginx:latest
```

---

### Verify Deployment

```bash
kubectl get deployments
```

Expected Output:

```text
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           10s
```

---

### Verify ReplicaSet

```bash
kubectl get rs
```

---

### Verify Pod

```bash
kubectl get pods
```

---

## 📄 YAML Manifest

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

spec:
  replicas: 1

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

Deploy:

```bash
kubectl apply -f deployment.yaml
```

---

# 📚 Key Concepts Learned

## 🚀 What is a Deployment?

A Deployment is a Kubernetes object used to manage Pods declaratively.

It provides:

✅ Pod Management
✅ Scaling
✅ Self-Healing
✅ Rolling Updates
✅ Rollbacks

---

## 📦 Deployment vs Pod

| Pod                            | Deployment                 |
| ------------------------------ | -------------------------- |
| Creates Containers Directly    | Manages Pods               |
| No Self-Healing                | Self-Healing               |
| Manual Scaling                 | Automatic Scaling          |
| Not Recommended for Production | Recommended for Production |

---

### Example

If a Pod is deleted:

```bash
kubectl delete pod nginx-xxxxx
```

With a Deployment:

```text
Deployment
    │
ReplicaSet
    │
Pod Deleted
    │
New Pod Created Automatically
```

Kubernetes automatically recreates the Pod.

---

## 🔄 ReplicaSet

A Deployment creates and manages a ReplicaSet.

ReplicaSet ensures the desired number of Pods are always running.

Example:

```yaml
replicas: 3
```

Kubernetes maintains exactly 3 Pods.

---

## 🎯 Selector and Labels

Deployment identifies its Pods using labels.

```yaml
selector:
  matchLabels:
    app: nginx
```

Pod Template:

```yaml
labels:
  app: nginx
```

The selector and labels must match.

---

## 🐳 Image Versioning

Task Requirement:

```yaml
image: nginx:latest
```

Production Best Practice:

```yaml
image: nginx:1.27.0
```

Benefits:

* Predictable deployments
* Easy rollback
* Version consistency

---

# 🛠️ Useful Commands

### Create Deployment

```bash
kubectl create deployment nginx --image=nginx:latest
```

### View Deployments

```bash
kubectl get deployments
```

### View ReplicaSets

```bash
kubectl get rs
```

### View Pods

```bash
kubectl get pods
```

### Describe Deployment

```bash
kubectl describe deployment nginx
```

### Scale Deployment

```bash
kubectl scale deployment nginx --replicas=3
```

### Delete Deployment

```bash
kubectl delete deployment nginx
```

---

# 🎤 Interview Explanation

### What did you do in this task?

> "In this task, I created a Kubernetes Deployment named `nginx` using the `nginx:latest` image. Instead of creating a Pod directly, I used a Deployment because it provides declarative management, self-healing, scaling, rolling updates, and rollback capabilities. The Deployment automatically created a ReplicaSet, which in turn created and managed the Pod running the Nginx container."

---

# 🔥 Interview Questions

### 1️⃣ What is a Deployment?

A Deployment is a Kubernetes resource that manages Pods declaratively and provides features like scaling, self-healing, rolling updates, and rollbacks.

---

### 2️⃣ Why use Deployment instead of Pod?

Because Pods are temporary.

Deployment provides:

* Automatic Pod recreation
* Scaling
* Updates
* Rollbacks

Production applications are almost always deployed through Deployments.

---

### 3️⃣ What is the relationship between Deployment, ReplicaSet, and Pod?

```text
Deployment
    │
ReplicaSet
    │
Pod
    │
Container
```

Deployment manages ReplicaSets, and ReplicaSets manage Pods.

---

### 4️⃣ What happens if a Pod is deleted?

The ReplicaSet detects the missing Pod and automatically creates a new one to maintain the desired state.

---

### 5️⃣ What is a ReplicaSet?

A ReplicaSet ensures the specified number of Pod replicas are always running.

Example:

```yaml
replicas: 3
```

If one Pod fails, another is created automatically.

---

### 6️⃣ What is Scaling?

Scaling increases or decreases the number of Pod replicas.

Example:

```bash
kubectl scale deployment nginx --replicas=5
```

---

### 7️⃣ What are Rolling Updates?

Rolling updates allow updating an application version without downtime.

Example:

```bash
kubectl set image deployment/nginx nginx=nginx:1.27.0
```

Pods are updated gradually.

---

### 8️⃣ What is Rollback?

Rollback reverts a Deployment to a previous version if an update fails.

Example:

```bash
kubectl rollout undo deployment nginx
```

---

### 9️⃣ How do you check rollout status?

```bash
kubectl rollout status deployment nginx
```

---

### 🔟 Why avoid `latest` in production?

Because it can lead to inconsistent deployments and difficult rollbacks.

Use versioned image tags instead.

---

# 🎯 Interview Takeaway

This task introduces one of the most important Kubernetes resources:

✅ Deployments
✅ ReplicaSets
✅ Self-Healing
✅ Scaling
✅ Rolling Updates
✅ Rollbacks
✅ Declarative Management

💡 **Key Interview Point:** A Pod runs an application, but a Deployment manages the application's lifecycle. In real-world Kubernetes environments, applications are deployed through Deployments rather than standalone Pods. 🚀
