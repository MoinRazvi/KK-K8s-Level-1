# 🚀 Kubernetes Level-1 | Task-01 | Create a Pod

## 🎯 Objective

Create a Kubernetes Pod with the following requirements:

| Parameter      | Value             |
| -------------- | ----------------- |
| Pod Name       | `pod-nginx`       |
| Image          | `nginx:latest`    |
| Container Name | `nginx-container` |
| Label          | `app=nginx_app`   |

---

## 🏗️ Architecture

```text
+-----------------------+
|      Pod              |
|   pod-nginx           |
|                       |
|  +-----------------+  |
|  | nginx-container |  |
|  | nginx:latest    |  |
|  +-----------------+  |
|                       |
| Label:               |
| app=nginx_app        |
+-----------------------+
```

---

## 📝 Solution

### Create Manifest File

**pod.yaml**

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: pod-nginx
  labels:
    app: nginx_app

spec:
  containers:
  - name: nginx-container
    image: nginx:latest
```

---

## 🚀 Deploy the Pod

```bash
kubectl apply -f pod.yaml
```

Expected Output:

```text
pod/pod-nginx created
```

---

## ✅ Verification

### Verify Pod Status

```bash
kubectl get pods
```

Example:

```text
NAME        READY   STATUS    RESTARTS   AGE
pod-nginx   1/1     Running   0          10s
```

### Verify Labels

```bash
kubectl get pods --show-labels
```

Expected:

```text
NAME        STATUS    LABELS
pod-nginx   Running   app=nginx_app
```

### Verify Container Name

```bash
kubectl get pod pod-nginx -o jsonpath='{.spec.containers[0].name}'
```

Expected:

```text
nginx-container
```

---

# 📚 Key Concepts Learned

## 📦 What is a Pod?

A Pod is the **smallest deployable unit** in Kubernetes.

A Pod can contain:

* One container (most common)
* Multiple containers (sidecar pattern)

All containers inside a Pod share:

* 🌐 Network Namespace
* 💾 Storage Volumes
* 🔄 Lifecycle

---

## 🏷️ What are Labels?

Labels are key-value pairs attached to Kubernetes objects.

Example:

```yaml
labels:
  app: nginx_app
```

Labels are commonly used for:

* Resource identification
* Service discovery
* Monitoring
* Deployment selection

---

## 📌 Container Name

Every container must have a unique name within a Pod.

```yaml
containers:
- name: nginx-container
```

Used for:

* Log collection
* Troubleshooting
* Multi-container communication

---

## 🐳 Understanding the Image

```yaml
image: nginx:latest
```

| Component | Meaning          |
| --------- | ---------------- |
| nginx     | Image Repository |
| latest    | Image Tag        |

### Production Best Practice

❌ Avoid:

```yaml
image: nginx:latest
```

✅ Prefer:

```yaml
image: nginx:1.27.0
```

Benefits:

* Predictable deployments
* Easy rollback
* Version control

---

# 🛠️ Useful kubectl Commands

### Create Resource

```bash
kubectl apply -f pod.yaml
```

### List Pods

```bash
kubectl get pods
```

### Show Labels

```bash
kubectl get pods --show-labels
```

### Describe Pod

```bash
kubectl describe pod pod-nginx
```

### View Logs

```bash
kubectl logs pod-nginx
```

### Delete Pod

```bash
kubectl delete pod pod-nginx
```

### Export YAML

```bash
kubectl get pod pod-nginx -o yaml
```

---

# 🎤 Interview Questions

### 1️⃣ What is a Pod in Kubernetes?

A Pod is the smallest deployable unit in Kubernetes that contains one or more containers sharing networking and storage resources.

---

### 2️⃣ Can a Pod contain multiple containers?

✅ Yes.

Examples:

* Application Container
* Logging Sidecar
* Monitoring Sidecar

All containers share the same Pod IP address.

---

### 3️⃣ What are Labels?

Labels are key-value pairs attached to Kubernetes resources for identification and grouping.

Example:

```yaml
labels:
  app: nginx_app
```

---

### 4️⃣ Why are Labels Important?

Labels are used by:

* Services
* Deployments
* Monitoring Tools
* Selectors

Example:

```yaml
selector:
  app: nginx_app
```

A Service uses selectors to identify target Pods.

---

### 5️⃣ Difference Between Pod Name and Container Name?

| Pod Name            | Container Name       |
| ------------------- | -------------------- |
| Identifies Pod      | Identifies Container |
| Unique in Namespace | Unique inside Pod    |

Example:

```yaml
metadata:
  name: pod-nginx

containers:
- name: nginx-container
```

---

### 6️⃣ What Happens When a Pod is Deleted?

🗑️ Kubernetes:

* Stops containers
* Removes Pod IP
* Deletes ephemeral container storage

Persistent data survives only if backed by volumes.

---

### 7️⃣ What Does `kubectl describe pod` Do?

Provides detailed information about:

* Events
* Labels
* Container Details
* Pod IP
* Assigned Node
* Resource Information

Used heavily for troubleshooting.

---

### 8️⃣ Why Should We Avoid the `latest` Tag?

Using `latest` can cause:

❌ Unpredictable deployments
❌ Difficult rollbacks
❌ Different versions across environments

Use versioned tags instead.

---

### 9️⃣ How Do You Check the Container Name?

```bash
kubectl get pod pod-nginx -o jsonpath='{.spec.containers[0].name}'
```

---

### 🔟 What Are the Different Pod States?

```text
Pending
Running
Succeeded
Failed
Unknown
```

---

# 🎯 Interview Takeaway

This simple task validates your understanding of:

✅ Pod Creation
✅ YAML Manifests
✅ Labels & Metadata
✅ Container Configuration
✅ Image Management
✅ kubectl Commands
✅ Kubernetes Fundamentals
