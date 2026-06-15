# 🚀 Kubernetes Level-1 | Task-04 | Create Pod with Resource Requests and Limits

## 🎯 Objective

Create a Pod with specific CPU and Memory requests and limits.

| Parameter      | Value             |
| -------------- | ----------------- |
| Pod Name       | `httpd-pod`       |
| Container Name | `httpd-container` |
| Image          | `httpd:latest`    |
| Memory Request | `15Mi`            |
| CPU Request    | `100m`            |
| Memory Limit   | `20Mi`            |
| CPU Limit      | `100m`            |

---

## 🏗️ Architecture

```text
+----------------------------------+
| Pod: httpd-pod                  |
|                                 |
|  +--------------------------+   |
|  | Container               |   |
|  | httpd-container         |   |
|  | httpd:latest            |   |
|  +--------------------------+   |
|                                 |
| Requests:                       |
|   Memory = 15Mi                |
|   CPU    = 100m                |
|                                 |
| Limits:                         |
|   Memory = 20Mi                |
|   CPU    = 100m                |
+----------------------------------+
```

---

## 📝 Solution

### pod.yaml

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: httpd-pod

spec:
  containers:
  - name: httpd-container
    image: httpd:latest

    resources:
      requests:
        memory: "15Mi"
        cpu: "100m"

      limits:
        memory: "20Mi"
        cpu: "100m"
```

---

## 🚀 Deploy Pod

```bash
kubectl apply -f pod.yaml
```

Expected Output:

```text
pod/httpd-pod created
```

---

## ✅ Verification

### Verify Pod

```bash
kubectl get pods
```

---

### Verify Resource Requests and Limits

```bash
kubectl describe pod httpd-pod
```

Expected:

```text
Limits:
  cpu:     100m
  memory:  20Mi

Requests:
  cpu:     100m
  memory:  15Mi
```

---

### Alternative Verification

```bash
kubectl get pod httpd-pod -o yaml
```

---

# 📚 Key Concepts Learned

## ⚙️ What are Resource Requests?

Requests define the **minimum resources** Kubernetes guarantees to a container.

Example:

```yaml
requests:
  memory: "15Mi"
  cpu: "100m"
```

Meaning:

* At least 15Mi Memory
* At least 0.1 CPU Core

must be available before scheduling the Pod.

---

## 🚦 What are Resource Limits?

Limits define the **maximum resources** a container can consume.

Example:

```yaml
limits:
  memory: "20Mi"
  cpu: "100m"
```

Meaning:

* Container cannot use more than 20Mi Memory
* Container cannot use more than 0.1 CPU Core

---

## 🧠 Kubernetes Scheduler and Requests

When scheduling a Pod:

```text
Node Capacity
      │
      ▼
Scheduler checks Requests
      │
      ▼
Schedules Pod on suitable Node
```

⚠️ Scheduler considers **requests**, not limits.

Example:

```yaml
requests:
  memory: 15Mi
```

Node must have at least 15Mi available.

---

## 💾 Memory Limits

Memory is a hard limit.

Example:

```yaml
limits:
  memory: 20Mi
```

If the container exceeds:

```text
20Mi
```

Kubernetes may terminate it with:

```text
OOMKilled
```

(Out Of Memory Killed)

---

## 🖥️ CPU Limits

CPU behaves differently.

Example:

```yaml
limits:
  cpu: 100m
```

If application requires more CPU:

```text
Container is throttled
```

Instead of being killed.

---

## 🔢 Understanding CPU Units

```text
1000m = 1 CPU Core
500m  = 0.5 CPU
100m  = 0.1 CPU
```

Task Requirement:

```yaml
cpu: 100m
```

Meaning:

```text
0.1 CPU Core
```

---

## 📏 Understanding Memory Units

| Unit | Meaning  |
| ---- | -------- |
| Ki   | Kibibyte |
| Mi   | Mebibyte |
| Gi   | Gibibyte |

Examples:

```yaml
memory: 15Mi
memory: 512Mi
memory: 1Gi
```

---

# 🛠️ Useful Commands

### Describe Pod

```bash
kubectl describe pod httpd-pod
```

### View YAML

```bash
kubectl get pod httpd-pod -o yaml
```

### Check Resource Usage

```bash
kubectl top pod
```

*(Requires Metrics Server)*

### Delete Pod

```bash
kubectl delete pod httpd-pod
```

---

# 🎤 Interview Explanation

### What did you do in this task?

> "In this task, I created a Pod named `httpd-pod` running an Apache HTTP Server container using the `httpd:latest` image. I configured resource requests and limits at the container level. The requests were set to 15Mi memory and 100m CPU, ensuring the scheduler reserves those resources before placing the Pod on a node. The limits were set to 20Mi memory and 100m CPU to prevent the container from consuming excessive resources and impacting other workloads running in the cluster."

---

# 🔥 Interview Questions

### 1️⃣ What is the difference between Requests and Limits?

| Requests                     | Limits                    |
| ---------------------------- | ------------------------- |
| Minimum Guaranteed Resources | Maximum Allowed Resources |
| Used by Scheduler            | Enforced by Kubelet       |
| Determines Placement         | Controls Consumption      |

---

### 2️⃣ Why are Resource Requests important?

Requests help the scheduler determine whether a node has sufficient resources to run a Pod.

Without requests:

* Resource contention may occur
* Cluster utilization becomes unpredictable

---

### 3️⃣ Why are Resource Limits important?

Limits prevent a container from consuming excessive CPU or memory and affecting other applications on the same node.

---

### 4️⃣ What happens if Memory Limit is exceeded?

The container is terminated.

Status may show:

```text
OOMKilled
```

---

### 5️⃣ What happens if CPU Limit is exceeded?

The container is not killed.

Instead:

```text
CPU throttling occurs
```

The application may become slower.

---

### 6️⃣ Which resource does Kubernetes Scheduler use?

✅ Requests

Scheduler ignores limits when deciding node placement.

---

### 7️⃣ What does 100m CPU mean?

```text
1000m = 1 CPU Core
100m  = 0.1 CPU Core
```

---

### 8️⃣ Can Requests be greater than Limits?

❌ No

Example:

```yaml
requests:
  memory: 500Mi

limits:
  memory: 200Mi
```

Invalid configuration.

Requests must be less than or equal to limits.

---

### 9️⃣ What is OOMKilled?

OOMKilled stands for:

```text
Out Of Memory Killed
```

It occurs when a container exceeds its memory limit.

---

### 🔟 How do you check Pod Resource Configuration?

```bash
kubectl describe pod httpd-pod
```

or

```bash
kubectl get pod httpd-pod -o yaml
```

---

# 🎯 Interview Takeaway

This task introduces a critical production Kubernetes concept:

✅ Resource Requests
✅ Resource Limits
✅ CPU Management
✅ Memory Management
✅ Kubernetes Scheduling
✅ OOMKilled
✅ CPU Throttling

💡 **Key Interview Point:** Requests determine **where a Pod can run**, while Limits determine **how much a Pod can consume**. Proper resource management is essential to prevent noisy-neighbor problems and ensure stable cluster performance. 🚀
