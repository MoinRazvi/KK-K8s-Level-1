# 🚀 Kubernetes Level-1 | Task-11 | Troubleshoot a Multi-Container Pod

## 🎯 Objective

Troubleshoot the existing Pod `webserver` and bring it to the **Running** state.

### Existing Components

| Component         | Value               |
| ----------------- | ------------------- |
| Pod Name          | `webserver`         |
| Main Container    | `nginx-container`   |
| Main Image        | `nginx:latest`      |
| Sidecar Container | `sidecar-container` |
| Sidecar Image     | `ubuntu:latest`     |
| Shared Volume     | `shared-logs`       |

---

# ❌ Issue Identified

## Pod Status

```bash
kubectl get pods
```

Output:

```text
NAME        READY   STATUS             RESTARTS
webserver   1/2     ImagePullBackOff   0
```

---

## Describe the Pod

```bash
kubectl describe pod webserver
```

Important output:

```text
Image: nginx:latests

Reason: ImagePullBackOff

Message:

failed to pull image
docker.io/library/nginx:latests
not found
```

---

# 🔍 Root Cause

The main container image was configured incorrectly.

### Wrong

```yaml
image: nginx:latests
```

### Correct

```yaml
image: nginx:latest
```

Notice:

```text
latests ❌

latest  ✅
```

There is an extra:

```text
s
```

in the tag.

---

# 🚨 Error Flow

```text
nginx:latests
        │
        ▼

Docker Hub Search

docker.io/library/nginx:latests

        │
        ▼

Image Not Found

        │
        ▼

ErrImagePull

        │
        ▼

ImagePullBackOff
```

---

# 🛠️ Solution

## Export YAML

```bash
kubectl get pod webserver -o yaml > webserver.yaml
```

---

## Delete Existing Pod

```bash
kubectl delete pod webserver
```

---

## Create Correct YAML

### webserver.yaml

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: webserver
  labels:
    app: web-app

spec:

  containers:

  - name: nginx-container
    image: nginx:latest

    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/nginx

  - name: sidecar-container
    image: ubuntu:latest

    command:
    - sh
    - -c
    - while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done

    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/nginx

  volumes:
  - name: shared-logs
    emptyDir: {}
```

---

## Apply the YAML

```bash
kubectl apply -f webserver.yaml
```

Expected:

```text
pod/webserver created
```

---

# ✅ Verification

### Check Pod

```bash
kubectl get pods
```

Expected:

```text
NAME        READY   STATUS
webserver   2/2     Running
```

---

### Describe Pod

```bash
kubectl describe pod webserver
```

Expected:

```text
Containers:

nginx-container:
Image: nginx:latest
Ready: True


sidecar-container:
Image: ubuntu:latest
Ready: True
```

---

### Check Images

```bash
kubectl get pod webserver \
-o=jsonpath='{.spec.containers[*].image}'
```

Expected:

```text
nginx:latest ubuntu:latest
```

---

# 🏗️ Architecture

```text
                 Pod
              webserver
                   │
      ┌────────────┴────────────┐
      │                         │

nginx-container         sidecar-container

nginx:latest            ubuntu:latest

      │                         │
      └────────────┬────────────┘
                   │

            shared-logs

           EmptyDir Volume

             /var/log/nginx
```

---

# 📚 Key Concepts Learned

## What is ImagePullBackOff?

When Kubernetes cannot pull an image:

```text
Wrong Image

      ↓

ErrImagePull

      ↓

ImagePullBackOff
```

Kubernetes waits and retries with increasing intervals.

---

## Difference Between ErrImagePull and ImagePullBackOff

| ErrImagePull            | ImagePullBackOff                 |
| ----------------------- | -------------------------------- |
| First image pull failed | Kubernetes retries after backoff |
| Immediate error         | Delayed retries                  |
| Wrong image name/tag    | Usually follows ErrImagePull     |

---

### Example

```text
nginx:latests ❌
```

Results in:

```text
ErrImagePull

↓

ImagePullBackOff
```

---

## Common Causes of ImagePullBackOff

### Wrong Image Name

```text
nginxs:latest ❌

nginx:latest ✅
```

---

### Wrong Tag

```text
nginx:latests ❌

nginx:latest ✅
```

---

### Private Registry Authentication Failure

```text
ImagePullBackOff
```

because:

```text
No imagePullSecrets configured
```

---

### Network Issues

Kubernetes cannot reach:

```text
docker.io
```

or private registry.

---

# 🔥 Troubleshooting Commands

### Check Pod Status

```bash
kubectl get pods
```

---

### Describe Pod

```bash
kubectl describe pod webserver
```

---

### View Logs

```bash
kubectl logs webserver -c nginx-container
```

Sidecar:

```bash
kubectl logs webserver -c sidecar-container
```

---

### Export YAML

```bash
kubectl get pod webserver -o yaml
```

---

### Check Images Only

```bash
kubectl get pod webserver \
-o=jsonpath='{.spec.containers[*].image}'
```

---

# 🎤 Interview Explanation

> "In this troubleshooting task, the Pod `webserver` was stuck in the `ImagePullBackOff` state. By using `kubectl describe pod`, I identified that the main container image was incorrectly configured as `nginx:latests`. Since this image tag does not exist on Docker Hub, Kubernetes failed to pull the image and repeatedly retried, resulting in `ImagePullBackOff`. I corrected the image to `nginx:latest`, recreated the Pod, and verified that both containers reached the Running state."

---

# 🔥 Interview Questions

### 1️⃣ What is `ErrImagePull`?

`ErrImagePull` means Kubernetes failed to pull the container image.

---

### 2️⃣ What is `ImagePullBackOff`?

After `ErrImagePull`, Kubernetes retries pulling the image with increasing delay intervals.

---

### 3️⃣ What command do you use first for Pod troubleshooting?

```bash
kubectl describe pod <pod-name>
```

This is the **most common first troubleshooting command**.

---

### 4️⃣ How do you check which image a Pod is using?

```bash
kubectl get pod webserver \
-o=jsonpath='{.spec.containers[*].image}'
```

---

### 5️⃣ What are common reasons for `ImagePullBackOff`?

* Wrong image name
* Wrong image tag
* Private registry authentication issues
* Network/DNS issues

---

### 6️⃣ Can a Pod be Running if one container fails?

❌ No

Example:

```text
nginx-container   → Failed

sidecar-container → Running

Pod → Not Ready
```

All containers must be healthy for the Pod to become:

```text
READY 2/2
STATUS Running
```

---

# 📌 Task Summary (Interview Revision)

### Key Learnings

✅ Multi-Container Pod Troubleshooting
✅ `ErrImagePull`
✅ `ImagePullBackOff`
✅ Shared Volumes (`emptyDir`)
✅ Sidecar Container Pattern
✅ Pod Logs and Events
✅ YAML Export and Recreation

💡 **Most Important Interview Point:**
When troubleshooting Pods, always follow this sequence:

```bash
kubectl get pods

kubectl describe pod <pod>

kubectl logs <pod> -c <container>

kubectl get pod <pod> -o yaml
```

These four commands solve a large percentage of Kubernetes troubleshooting scenarios and are frequently discussed in DevOps interviews. 🚀
