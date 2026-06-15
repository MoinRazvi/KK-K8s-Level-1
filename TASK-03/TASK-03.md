# 🚀 Kubernetes Level-1 | Task-03 | Create Namespace and Pod

## 🎯 Objective

Create a Kubernetes Namespace and deploy a Pod inside it.

| Parameter | Value           |
| --------- | --------------- |
| Namespace | `dev`           |
| Pod Name  | `dev-nginx-pod` |
| Image     | `nginx:latest`  |

---

## 🏗️ Architecture

```text
Kubernetes Cluster
│
├── default Namespace
│
├── kube-system Namespace
│
└── dev Namespace
      │
      └── Pod
           │
           └── nginx:latest
```

---

## 📝 Solution

### Step 1: Create Namespace

```bash
kubectl create namespace dev
```

Expected Output:

```text
namespace/dev created
```

---

### Step 2: Create Pod in Namespace

```bash
kubectl run dev-nginx-pod \
--image=nginx:latest \
--restart=Never \
-n dev
```

---

## ✅ Verification

### Verify Namespace

```bash
kubectl get ns
```

Expected:

```text
NAME
default
dev
kube-system
```

---

### Verify Pod

```bash
kubectl get pods -n dev
```

Expected:

```text
NAME            READY   STATUS
dev-nginx-pod   1/1     Running
```

---

### Detailed Verification

```bash
kubectl describe pod dev-nginx-pod -n dev
```

---

## 📄 YAML Manifest Method

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
---
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
  namespace: dev

spec:
  containers:
  - name: nginx
    image: nginx:latest
```

Deploy:

```bash
kubectl apply -f namespace-pod.yaml
```

---

# 📚 Key Concepts Learned

## 🏢 What is a Namespace?

A Namespace is a logical partition inside a Kubernetes cluster.

It helps:

✅ Isolate resources
✅ Organize workloads
✅ Avoid naming conflicts
✅ Apply security policies
✅ Allocate resources

---

## 🌍 Why Do We Need Namespaces?

Imagine multiple teams using the same cluster:

```text
Development Team
    │
    └── Namespace: dev

Testing Team
    │
    └── Namespace: test

Production Team
    │
    └── Namespace: prod
```

All teams can create:

```text
nginx-pod
```

without conflicts because they exist in different namespaces.

---

## 📦 Namespace Scope

Pods are namespace-scoped resources.

Example:

```bash
kubectl get pods
```

Shows Pods only in the current namespace.

To view Pods in a specific namespace:

```bash
kubectl get pods -n dev
```

To view all Pods:

```bash
kubectl get pods -A
```

---

## 🎯 Default Namespace

When no namespace is specified:

```bash
kubectl run nginx --image=nginx
```

Kubernetes creates the Pod in:

```text
default
```

namespace.

In this task we explicitly used:

```bash
-n dev
```

to place the Pod inside the `dev` namespace.

---

## 🔍 Important Namespace Commands

### Create Namespace

```bash
kubectl create ns dev
```

### List Namespaces

```bash
kubectl get ns
```

### View Pods in Namespace

```bash
kubectl get pods -n dev
```

### Set Current Namespace

```bash
kubectl config set-context --current --namespace=dev
```

### Delete Namespace

```bash
kubectl delete ns dev
```

⚠️ Deleting a namespace deletes all resources inside it.

---

# 🎤 Interview Explanation

### What did you do in this task?

> "In this task, I created a namespace named `dev` and deployed a Pod called `dev-nginx-pod` inside that namespace using the `nginx:latest` image. The purpose of using namespaces is to logically isolate resources within a Kubernetes cluster. Namespaces help multiple teams or environments such as development, testing, and production share the same cluster without resource naming conflicts."

---

# 🔥 Interview Questions

### 1️⃣ What is a Namespace in Kubernetes?

A Namespace is a logical isolation mechanism used to organize and separate resources within a Kubernetes cluster.

---

### 2️⃣ Why are Namespaces used?

Namespaces are used for:

* Multi-tenancy
* Resource isolation
* Environment separation
* Security and access control

---

### 3️⃣ What is the default Namespace?

The default namespace is:

```text
default
```

Resources are created there if no namespace is specified.

---

### 4️⃣ Can two Pods have the same name?

✅ Yes, if they exist in different namespaces.

Example:

```text
dev/nginx-pod
prod/nginx-pod
```

Both are valid.

---

### 5️⃣ Are Pods cluster-scoped or namespace-scoped?

Pods are namespace-scoped resources.

Examples:

* Pods ✅
* Deployments ✅
* Services ✅

Cluster-scoped examples:

* Nodes
* Namespaces
* PersistentVolumes

---

### 6️⃣ How do you list all Pods in all namespaces?

```bash
kubectl get pods -A
```

or

```bash
kubectl get pods --all-namespaces
```

---

### 7️⃣ How do you create a resource in a specific namespace?

```bash
kubectl run nginx \
--image=nginx:latest \
-n dev
```

Or in YAML:

```yaml
metadata:
  namespace: dev
```

---

### 8️⃣ What happens if a Namespace is deleted?

All resources inside that namespace are deleted automatically.

Example:

```bash
kubectl delete namespace dev
```

Deletes:

* Pods
* Deployments
* Services
* ConfigMaps
* Secrets

inside the namespace.

---

### 9️⃣ How do you check your current namespace?

```bash
kubectl config view --minify | grep namespace
```

---

### 🔟 What is the difference between Namespace and Cluster?

| Namespace                | Cluster                       |
| ------------------------ | ----------------------------- |
| Logical Isolation        | Entire Kubernetes Environment |
| Shares Cluster Resources | Contains All Resources        |
| Lightweight              | Infrastructure Level          |

---

# 🎯 Interview Takeaway

This task introduces one of the most important concepts for real-world Kubernetes environments:

✅ Namespaces
✅ Resource Isolation
✅ Multi-Tenancy
✅ Environment Separation (Dev/Test/Prod)
✅ Namespace-Scoped Resources
✅ Cluster Organization

💡 **Key Interview Point:** A Pod tells Kubernetes *what to run*, while a Namespace tells Kubernetes *where to organize and isolate it*. In enterprise environments, almost every application is deployed inside dedicated namespaces rather than the default namespace. 🚀
