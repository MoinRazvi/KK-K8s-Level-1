# 🚀 Kubernetes Level-1 | Task-12 | Update Existing Deployment & Service

## 🎯 Objective

Without deleting the existing resources:

| Resource   | Change                              |
| ---------- | ----------------------------------- |
| Service    | NodePort `30008` → `32165`          |
| Deployment | Replicas `1` → `5`                  |
| Deployment | Image `nginx:1.17` → `nginx:latest` |

---

# ✅ Step 1: Verify Existing Resources

```bash
kubectl get deploy
kubectl get svc
```

Expected:

```text
NAME                READY
nginx-deployment    1/1

NAME             TYPE       PORT(S)
nginx-service    NodePort   80:30008/TCP
```

---

# ✅ Step 2: Change the NodePort

First edit the service:

```bash
kubectl edit svc nginx-service
```

Find:

```yaml
nodePort: 30008
```

Replace:

```yaml
nodePort: 32165
```

Save and exit.

---

### Verify

```bash
kubectl get svc nginx-service
```

Expected:

```text
NAME            TYPE       PORT(S)
nginx-service   NodePort   80:32165/TCP
```

---

# ✅ Step 3: Scale Deployment

Change replicas:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

---

### Verify

```bash
kubectl get deploy
```

Expected:

```text
NAME                READY
nginx-deployment    5/5
```

Check Pods:

```bash
kubectl get pods
```

Expected:

```text
nginx-deployment-xxxxx   Running
nginx-deployment-yyyyy   Running
nginx-deployment-zzzzz   Running
nginx-deployment-aaaaa   Running
nginx-deployment-bbbbb   Running
```

---

# ✅ Step 4: Update Image

Before updating, check container name:

```bash
kubectl describe deployment nginx-deployment
```

Look for:

```text
Containers:

nginx-container:
```

Now update:

```bash
kubectl set image deployment/nginx-deployment \
nginx-container=nginx:latest
```

---

### Verify Rolling Update

```bash
kubectl rollout status deployment nginx-deployment
```

Expected:

```text
deployment "nginx-deployment" successfully rolled out
```

---

### Verify Image

```bash
kubectl get deployment nginx-deployment \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

Expected:

```text
nginx:latest
```

---

# 🏗️ Architecture

```text
                Service

          nginx-service

      NodePort: 32165

                 │

                 ▼

          nginx-deployment

          Replicas = 5

     ┌────────┬────────┬────────┬────────┬────────┐
     │        │        │        │        │
     ▼        ▼        ▼        ▼        ▼

   Pod1     Pod2     Pod3     Pod4     Pod5

        nginx:latest
```

---

# 📝 Quick Commands (Exam Version)

```bash
kubectl edit svc nginx-service

# change:
nodePort: 32165


kubectl scale deployment nginx-deployment --replicas=5


kubectl set image deployment/nginx-deployment \
nginx-container=nginx:latest


kubectl rollout status deployment nginx-deployment
```

---

# ✅ Final Verification Commands

### Check Deployment

```bash
kubectl get deploy
```

---

### Check Service

```bash
kubectl get svc
```

---

### Check Pods

```bash
kubectl get pods
```

---

### Check Image

```bash
kubectl get deployment nginx-deployment \
-o=jsonpath='{.spec.template.spec.containers[*].image}'
```

---

### Check NodePort

```bash
kubectl get svc nginx-service \
-o=jsonpath='{.spec.ports[*].nodePort}'
```

Expected:

```text
32165
```

---

# 🎤 Interview Explanation

> "In this task, I updated an existing Deployment and Service without deleting them. I modified the Service NodePort from `30008` to `32165`, scaled the Deployment replicas from `1` to `5`, and performed a rolling image update from `nginx:1.17` to `nginx:latest`. Kubernetes Deployment handled the rolling update by gradually replacing old Pods with new Pods while maintaining application availability."

---

# 🔥 Interview Questions

### 1️⃣ How do you scale a Deployment?

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

---

### 2️⃣ How do you update a Deployment image?

```bash
kubectl set image deployment/nginx-deployment \
nginx-container=nginx:latest
```

---

### 3️⃣ Does updating the image recreate the Deployment?

❌ No

Kubernetes performs:

✅ Rolling Update

which:

```text
Old Pods ↓

New Pods ↑

No Downtime
```

---

### 4️⃣ How do you verify a rolling update?

```bash
kubectl rollout status deployment nginx-deployment
```

---

### 5️⃣ What is NodePort?

A Service type that exposes the application on:

```text
<NodeIP>:<NodePort>
```

Example:

```text
10.0.0.10:32165
```

---

### 6️⃣ NodePort Range?

Default:

```text
30000 - 32767
```

So:

```text
32165 ✅ Valid
```

---

### 7️⃣ Difference between ClusterIP and NodePort?

| ClusterIP                      | NodePort                   |
| ------------------------------ | -------------------------- |
| Internal access                | External access            |
| Default Service Type           | Exposes Node Port          |
| Accessible only inside cluster | Accessible outside cluster |

---

# 📌 Task Summary (Interview Revision)

### Key Learnings

✅ Update Existing Service
✅ Change NodePort
✅ Scale Deployment
✅ Rolling Image Update
✅ Verify Rollout Status
✅ Deployment Scaling
✅ Service Types

💡 **Most Important Interview Point:**
A Deployment allows **zero-downtime updates** through **Rolling Updates**, where old Pods are gradually replaced with new Pods while ensuring the application remains available throughout the upgrade process. 🚀

#
If you want to **copy the existing YAML of a Kubernetes resource, modify it, and reapply it**, these are the most common commands.

---

## 📥 Copy Deployment YAML to a File

```bash
kubectl get deployment nginx-deployment -o yaml > nginx-deployment.yaml
```

---

## 📥 Copy Service YAML to a File

```bash
kubectl get svc nginx-service -o yaml > nginx-service.yaml
```

---

## ✏️ Modify the YAML

Using `vi`:

```bash
vi nginx-deployment.yaml
```

or

```bash
vi nginx-service.yaml
```

---

## ⚠️ Important Before Reapplying

When you export a resource using:

```bash
kubectl get deployment nginx-deployment -o yaml
```

the file contains extra fields like:

```yaml
creationTimestamp:
resourceVersion:
uid:
managedFields:
status:
```

These fields are **generated by Kubernetes** and should be removed if you're recreating a resource from scratch.

---

## 📋 Quick Way (Exam/KodeKloud Friendly)

Instead of editing exported YAML, use:

```bash
kubectl edit deployment nginx-deployment
```

or

```bash
kubectl edit svc nginx-service
```

This opens the live configuration directly in the editor.

---

## 📄 Copy Existing Pod YAML

```bash
kubectl get pod webserver -o yaml > webserver.yaml
```

Edit:

```bash
vi webserver.yaml
```

Then:

```bash
kubectl delete pod webserver
kubectl apply -f webserver.yaml
```

---

## 🎯 Useful Copy Commands for GitHub Notes

### Deployment

```bash
kubectl get deploy nginx-deployment -o yaml > deployment.yaml
```

### Service

```bash
kubectl get svc nginx-service -o yaml > service.yaml
```

### Pod

```bash
kubectl get pod webserver -o yaml > pod.yaml
```

### ReplicaSet

```bash
kubectl get rs httpd-replicaset -o yaml > rs.yaml
```

### CronJob

```bash
kubectl get cronjob nautilus -o yaml > cronjob.yaml
```

### Job

```bash
kubectl get job countdown-devops -o yaml > job.yaml
```

---

## 🚀 Senior Engineer Tip

For troubleshooting or backups, I usually follow:

```bash
kubectl get <resource> <name> -o yaml > backup.yaml
```

Example:

```bash
kubectl get deploy nginx-deployment -o yaml > backup-nginx.yaml
```

This gives you:

* Backup of the current configuration
* Easy comparison using `diff`
* Version control in Git
* Quick rollback if required

This is a common practice in production Kubernetes environments and is frequently appreciated in DevOps interviews.
