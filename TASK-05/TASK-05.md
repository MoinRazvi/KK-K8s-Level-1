# 🚀 Kubernetes Level-1 | Task-05 | Rolling Update Deployment

## 🎯 Objective

Perform a rolling update on an existing Deployment.

| Parameter       | Value              |
| --------------- | ------------------ |
| Deployment Name | `nginx-deployment` |
| Container Name  | `nginx-container`  |
| Current Image   | `nginx:1.16`       |
| New Image       | `nginx:1.17`       |
| Update Strategy | Rolling Update     |

---

## 🏗️ Architecture

### Before Update

```text
Deployment (nginx-deployment)
        │
        ▼
   ReplicaSet (v1)
        │
 ┌──────────────────┐
 │ nginx-container  │
 │ nginx:1.16       │
 └──────────────────┘
```

### During Rolling Update

```text
Deployment
      │
 ┌────┴────┐
 │         │
 ▼         ▼
Old RS   New RS
(1.16)   (1.17)
 │         │
 ▼         ▼
Pods     Pods
```

### After Update

```text
Deployment (nginx-deployment)
        │
        ▼
   ReplicaSet (v2)
        │
 ┌──────────────────┐
 │ nginx-container  │
 │ nginx:1.17       │
 └──────────────────┘
```

---

# 📝 Solution

## Verify Existing Deployment

```bash
kubectl describe deployment nginx-deployment
```

Output showed:

```text
Container Name : nginx-container
Image          : nginx:1.16
```

---

## Perform Rolling Update

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.17
```

Expected Output:

```text
deployment.apps/nginx-deployment image updated
```

---

## Monitor Rollout

```bash
kubectl rollout status deployment/nginx-deployment
```

Expected:

```text
deployment "nginx-deployment" successfully rolled out
```

---

## Verify Updated Image

```bash
kubectl describe deployment nginx-deployment
```

or

```bash
kubectl get deployment nginx-deployment -o yaml | grep image
```

Expected:

```text
image: nginx:1.17
```

---

## Verify Pods

```bash
kubectl get pods
```

Expected:

```text
NAME                               READY   STATUS
nginx-deployment-xxxxx             1/1     Running
nginx-deployment-yyyyy             1/1     Running
nginx-deployment-zzzzz             1/1     Running
```

---

# 📚 Key Concepts Learned

## 🔄 What is a Rolling Update?

A Rolling Update gradually replaces old Pods with new Pods without bringing down the application.

### Traditional Update

```text
Stop Old Pods
      ↓
Start New Pods
```

❌ Downtime occurs

---

### Rolling Update

```text
Start New Pod
      ↓
Verify Health
      ↓
Remove Old Pod
      ↓
Repeat
```

✅ Zero Downtime

---

## 🚀 Why Use Rolling Updates?

Benefits:

* Zero Downtime Deployments
* Safer Releases
* Controlled Rollout
* Easy Rollback
* Continuous Availability

---

## 📦 Deployment → ReplicaSet → Pods

```text
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
```

A Deployment never updates Pods directly.

Instead:

1. Creates a new ReplicaSet
2. Creates new Pods
3. Deletes old Pods gradually

---

## 🔍 Important Observation from this Task

The deployment name was:

```text
nginx-deployment
```

But the container name was:

```text
nginx-container
```

### Incorrect Command

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.17
```

Result:

```text
error: unable to find container named "nginx"
```

---

### Correct Command

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.17
```

Reason:

`kubectl set image` requires:

```text
<container-name>=<new-image>
```

NOT

```text
<deployment-name>=<new-image>
```

This is a very common Kubernetes interview question.

---

## 🔙 Rollback

If deployment fails:

```bash
kubectl rollout undo deployment/nginx-deployment
```

Kubernetes restores the previous version.

---

## 📜 Deployment History

View revisions:

```bash
kubectl rollout history deployment/nginx-deployment
```

Example:

```text
REVISION
1
2
3
```

---

# 🛠️ Useful Commands

### Update Image

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.17
```

### Monitor Rollout

```bash
kubectl rollout status deployment/nginx-deployment
```

### View Rollout History

```bash
kubectl rollout history deployment/nginx-deployment
```

### Rollback Deployment

```bash
kubectl rollout undo deployment/nginx-deployment
```

### Describe Deployment

```bash
kubectl describe deployment nginx-deployment
```

### Check Pods

```bash
kubectl get pods
```

---

# 🎤 Interview Explanation

> "In this task, I performed a rolling update on the `nginx-deployment` Deployment. The existing Pods were running the `nginx:1.16` image, and I upgraded them to `nginx:1.17` using the `kubectl set image` command. Kubernetes automatically created a new ReplicaSet and gradually replaced the old Pods with new Pods while maintaining application availability. I also verified the rollout status and ensured all Pods were in the Running state after the update."

---

# 🔥 Interview Questions

### 1️⃣ What is a Rolling Update?

A deployment strategy where Pods are updated gradually without causing application downtime.

---

### 2️⃣ Why use Rolling Updates?

* Zero Downtime
* Safer Deployments
* Easy Rollback
* Continuous Service Availability

---

### 3️⃣ What command performs a rolling update?

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.17
```

---

### 4️⃣ What happens internally during a rolling update?

1. New ReplicaSet is created.
2. New Pods are launched.
3. Health checks are performed.
4. Old Pods are terminated gradually.

---

### 5️⃣ Why did the command fail when using `nginx=nginx:1.17`?

Because `nginx` was not the container name.

The actual container name was:

```text
nginx-container
```

`kubectl set image` requires the exact container name.

---

### 6️⃣ How do you check the container name in a Deployment?

```bash
kubectl describe deployment nginx-deployment
```

or

```bash
kubectl get deployment nginx-deployment -o yaml
```

---

### 7️⃣ What is the relationship between Deployment and ReplicaSet?

```text
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
```

Deployment manages ReplicaSets, and ReplicaSets manage Pods.

---

### 8️⃣ How do you monitor deployment progress?

```bash
kubectl rollout status deployment/nginx-deployment
```

---

### 9️⃣ How do you rollback a deployment?

```bash
kubectl rollout undo deployment/nginx-deployment
```

---

### 🔟 Difference Between Recreate and Rolling Update?

| Recreate             | Rolling Update          |
| -------------------- | ----------------------- |
| Stops all Pods first | Gradually replaces Pods |
| Downtime             | No Downtime             |
| Faster               | Safer                   |
| Rarely used          | Default Strategy        |

---

# 📌 Task Summary (Interview Revision)

This task introduced **Rolling Updates**, one of the most important production deployment strategies in Kubernetes.

### Key Learnings

✅ Updating container images in Deployments
✅ Understanding Deployment → ReplicaSet → Pod hierarchy
✅ Performing Zero-Downtime Deployments
✅ Monitoring Rollouts
✅ Deployment Rollbacks
✅ Difference between Deployment Name and Container Name
✅ Troubleshooting `kubectl set image` failures

💡 **Most Important Interview Point:**
When using `kubectl set image`, Kubernetes expects the **container name**, not the Deployment name. Always verify the container name before updating an image. This is a common real-world troubleshooting scenario and interview question. 🚀
