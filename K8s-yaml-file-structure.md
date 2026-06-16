# 🚀 Kubernetes YAML Structure

Almost every Kubernetes manifest follows this structure:

```yaml
apiVersion:
kind:
metadata:
spec:
```

Think of it as:

| Field        | Purpose                                        |
| ------------ | ---------------------------------------------- |
| `apiVersion` | Which Kubernetes API should handle this object |
| `kind`       | What type of resource to create                |
| `metadata`   | Information that identifies the object         |
| `spec`       | Desired state/configuration of the object      |

---

# 🏗️ General Structure

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment
  labels:
    app: nginx

spec:
  replicas: 3

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

---

# 1️⃣ apiVersion

This tells Kubernetes:

> "Which API group and version should interpret this object?"

Examples:

| Resource    | apiVersion             |
| ----------- | ---------------------- |
| Pod         | `v1`                   |
| Service     | `v1`                   |
| ConfigMap   | `v1`                   |
| Secret      | `v1`                   |
| Namespace   | `v1`                   |
| ReplicaSet  | `apps/v1`              |
| Deployment  | `apps/v1`              |
| DaemonSet   | `apps/v1`              |
| StatefulSet | `apps/v1`              |
| Job         | `batch/v1`             |
| CronJob     | `batch/v1`             |
| Ingress     | `networking.k8s.io/v1` |

---

### Example

```yaml
apiVersion: v1
kind: Pod
```

means:

> Create a Pod using Core API Version 1.

---

# 2️⃣ kind

This tells Kubernetes:

> "What object do you want to create?"

Common kinds:

| Kind        | Purpose               |
| ----------- | --------------------- |
| Pod         | Runs containers       |
| ReplicaSet  | Maintains Pods        |
| Deployment  | Manages ReplicaSets   |
| Service     | Exposes Pods          |
| Namespace   | Logical isolation     |
| ConfigMap   | Store configurations  |
| Secret      | Store sensitive data  |
| Job         | One-time task         |
| CronJob     | Scheduled task        |
| StatefulSet | Stateful applications |
| DaemonSet   | One Pod per Node      |
| Ingress     | HTTP/HTTPS Routing    |

---

### Example

```yaml
kind: Deployment
```

means:

Create a Deployment object.

---

# 3️⃣ metadata

This is the identity card of the object.

Example:

```yaml
metadata:
  name: nginx-deployment

  namespace: dev

  labels:
    app: nginx

  annotations:
    description: Frontend App
```

---

### Common Metadata Fields

| Field             | Purpose           |
| ----------------- | ----------------- |
| name              | Resource Name     |
| namespace         | Namespace         |
| labels            | Key-value pairs   |
| annotations       | Extra information |
| uid               | Unique ID         |
| creationTimestamp | Creation time     |

---

### Example

```yaml
metadata:
  name: my-pod
```

Kubernetes identifies it as:

```text
default/my-pod
```

or

```text
dev/my-pod
```

if namespace is specified.

---

# 4️⃣ spec

This is the most important section.

It defines:

> "How should this resource behave?"

---

## Pod Spec

```yaml
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

---

## Deployment Spec

```yaml
spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    spec:
      containers:
      - name: nginx
        image: nginx
```

---

## Service Spec

```yaml
spec:
  selector:
    app: nginx

  ports:
  - port: 80

  type: ClusterIP
```

---

# Important Point

### spec is different for every resource

For example:

### Pod

```yaml
spec:
  containers:
```

---

### Deployment

```yaml
spec:
  replicas:
  selector:
  template:
```

---

### Service

```yaml
spec:
  selector:
  ports:
  type:
```

---

### CronJob

```yaml
spec:
  schedule:
  jobTemplate:
```

---

# YAML Hierarchy

Interviewers love this question.

Take Deployment:

```text
apiVersion
kind
metadata
spec
 ├── replicas
 ├── selector
 └── template
      ├── metadata
      └── spec
            └── containers
```

Notice:

```text
Deployment
    ↓
Pod Template
         ↓
      Containers
```

---

# 🎤 Interview Questions

## Q1. What are the mandatory fields in a Kubernetes YAML?

Answer:

```text
apiVersion
kind
metadata
spec
```

---

## Q2. What is apiVersion?

Answer:

apiVersion specifies which Kubernetes API version should process the object.

Example:

```yaml
apiVersion: apps/v1
```

---

## Q3. What is kind?

Answer:

kind defines the type of Kubernetes object.

Examples:

```text
Pod
Deployment
ReplicaSet
Service
CronJob
```

---

## Q4. What is metadata?

Answer:

Metadata uniquely identifies an object.

Contains:

* Name
* Namespace
* Labels
* Annotations

---

## Q5. What is spec?

Answer:

Spec defines the desired state of the object.

Examples:

* Number of replicas
* Container image
* Ports
* Resources
* Schedules

---

## Q6. Difference between Metadata and Spec?

| Metadata    | Spec          |
| ----------- | ------------- |
| Identity    | Desired State |
| Name        | Replicas      |
| Labels      | Containers    |
| Namespace   | Ports         |
| Annotations | Resources     |

---

## Q7. Is spec same for all resources?

❌ No

Example:

```text
Pod spec       → containers
Deployment     → replicas + selector + template
Service        → selector + ports
CronJob        → schedule + jobTemplate
```

---

## Q8. How do you know which apiVersion to use?

Command:

```bash
kubectl api-resources
```

Example:

```text
NAME          APIVERSION
pods          v1
deployments   apps/v1
cronjobs      batch/v1
```

---

## Q9. How do you see a resource YAML?

```bash
kubectl get deployment nginx -o yaml
```

or

```bash
kubectl get pod nginx -o yaml
```

---

## Q10. Why do we use YAML instead of kubectl commands?

Answer:

Because YAML is:

✅ Declarative
✅ Version Controlled (Git)
✅ Reusable
✅ Easy to Automate
✅ Infrastructure as Code (IaC)

---

# 🌟 Interview Summary (Most Important Answer)

If an interviewer asks:

**"Explain the structure of a Kubernetes YAML file."**

A strong answer is:

> "Every Kubernetes manifest is composed of four major sections: `apiVersion`, `kind`, `metadata`, and `spec`. `apiVersion` tells Kubernetes which API version to use, `kind` specifies the resource type, `metadata` contains identifying information such as name and labels, and `spec` defines the desired state of the resource. While the first three sections are almost common across resources, the `spec` section varies depending on the resource type such as Pod, Deployment, Service, or CronJob."

🎯 This is considered a **foundational Kubernetes interview question**, and being able to explain it clearly creates a strong impression in DevOps and Kubernetes interviews.
