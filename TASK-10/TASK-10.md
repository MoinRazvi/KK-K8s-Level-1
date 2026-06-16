# 🚀 Kubernetes Level-1 | Task-11 | Create Pod with ConfigMap, Environment Variable & Volume

## 🎯 Objective

Create:

✅ Namespace: `xfusion` *(already exists in most KodeKloud labs)*
✅ ConfigMap: `time-config`
✅ Pod: `time-check`
✅ Container: `time-check`
✅ Image: `busybox:latest`
✅ Environment Variable from ConfigMap
✅ Volume mounted at `/opt/data/time`
✅ Write logs to `/opt/data/time/time-check.log`

---

# 📝 Step 1: Create ConfigMap

```bash
kubectl create configmap time-config \
--from-literal=TIME_FREQ=11 \
-n xfusion
```

Verify:

```bash
kubectl get cm -n xfusion
```

Expected:

```text
NAME          DATA
time-config   1
```

---

# 📝 Step 2: Create Pod YAML

### time-check.yaml

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: time-check
  namespace: xfusion

spec:
  containers:
  - name: time-check
    image: busybox:latest

    command:
    - /bin/sh
    - -c
    - while true; do date >> /opt/data/time/time-check.log; sleep $TIME_FREQ; done

    env:
    - name: TIME_FREQ
      valueFrom:
        configMapKeyRef:
          name: time-config
          key: TIME_FREQ

    volumeMounts:
    - name: log-volume
      mountPath: /opt/data/time

  volumes:
  - name: log-volume
    emptyDir: {}
```

---

## 🚀 Deploy Pod

```bash
kubectl apply -f time-check.yaml
```

Expected:

```text
pod/time-check created
```

---

# ✅ Verification Commands

### Check Pod

```bash
kubectl get pods -n xfusion
```

Expected:

```text
NAME         READY   STATUS
time-check   1/1     Running
```

---

### Check ConfigMap

```bash
kubectl get cm -n xfusion
```

---

### Describe Pod

```bash
kubectl describe pod time-check -n xfusion
```

Verify:

* Image = busybox:latest
* Environment Variable = TIME_FREQ
* Volume = log-volume
* Mount Path = /opt/data/time

---

### Verify Environment Variable

```bash
kubectl exec -it time-check -n xfusion -- env | grep TIME_FREQ
```

Expected:

```text
TIME_FREQ=11
```

---

### Verify Log File

```bash
kubectl exec -it time-check -n xfusion -- cat /opt/data/time/time-check.log
```

Expected:

```text
Tue Jun 16 10:11:00 UTC 2026
Tue Jun 16 10:11:11 UTC 2026
Tue Jun 16 10:11:22 UTC 2026
```

Each line is written every:

```text
11 Seconds
```

because:

```text
TIME_FREQ=11
```

comes from the ConfigMap.

---

# 🏗️ Architecture

```text
ConfigMap
time-config
   │
   │ TIME_FREQ=11
   ▼

Pod (time-check)
      │
      ▼
Container (time-check)
busybox:latest
      │
      ├── ENV:
      │      TIME_FREQ=11
      │
      ├── Volume:
      │      log-volume
      │
      ▼
/opt/data/time
      │
      ▼
time-check.log
```

---

# 📚 Key Concepts Learned

## 🗂️ What is a ConfigMap?

ConfigMap stores application configuration separately from container images.

Example:

```yaml
data:
  TIME_FREQ: "11"
```

This allows:

* Changing configuration without rebuilding images.
* Reusing the same image in different environments.

---

## Environment Variable from ConfigMap

```yaml
env:
- name: TIME_FREQ
  valueFrom:
    configMapKeyRef:
      name: time-config
      key: TIME_FREQ
```

Meaning:

```text
TIME_FREQ=11
```

inside the container.

---

## What is emptyDir Volume?

```yaml
volumes:
- name: log-volume
  emptyDir: {}
```

An `emptyDir`:

* Is created when Pod starts.
* Exists as long as the Pod is running.
* Gets deleted when Pod is deleted.

---

## Volume Mount

```yaml
volumeMounts:
- name: log-volume
  mountPath: /opt/data/time
```

Anything written here:

```text
/opt/data/time/time-check.log
```

goes into:

```text
log-volume
```

---

# 🎤 Interview Explanation

> "In this task, I created a ConfigMap named `time-config` that stores the key `TIME_FREQ=11`. Then I created a Pod named `time-check` using the `busybox:latest` image. The container reads the TIME_FREQ value from the ConfigMap as an environment variable and executes a shell loop that writes timestamps to `/opt/data/time/time-check.log` every 11 seconds. I also configured an `emptyDir` volume named `log-volume` and mounted it inside the container at `/opt/data/time`."

---

# 🔥 Interview Questions

### 1️⃣ What is a ConfigMap?

A ConfigMap stores configuration data as key-value pairs and decouples configuration from application images.

---

### 2️⃣ Difference between ConfigMap and Secret?

| ConfigMap           | Secret            |
| ------------------- | ----------------- |
| Non-sensitive data  | Sensitive data    |
| Plain text          | Base64 encoded    |
| Environment configs | Passwords, Tokens |

---

### 3️⃣ How do you use ConfigMap as an Environment Variable?

```yaml
env:
- name: TIME_FREQ
  valueFrom:
    configMapKeyRef:
      name: time-config
      key: TIME_FREQ
```

---

### 4️⃣ What is an emptyDir volume?

A temporary storage volume that exists as long as the Pod is running.

---

### 5️⃣ What happens to emptyDir when Pod is deleted?

```text
Pod Deleted
     ↓
emptyDir Deleted
```

All data is lost.

---

### 6️⃣ Difference Between ConfigMap Mount and Environment Variable?

| Environment Variable | Volume Mount       |
| -------------------- | ------------------ |
| Available as env var | Available as file  |
| Easy for apps        | Useful for configs |
| Small configs        | Large configs      |

---

### 7️⃣ How do you verify environment variables inside a Pod?

```bash
kubectl exec -it time-check -n xfusion -- env
```

---

### 8️⃣ How do you verify mounted files?

```bash
kubectl exec -it time-check -n xfusion -- ls /opt/data/time
```

---

### 🔟 Why externalize configuration using ConfigMap?

Because:

✅ Reusable Images
✅ Environment Specific Configurations
✅ Easier Updates
✅ Better Separation of Concerns

---

# 📌 Task Summary (Interview Revision)

### Key Learnings

✅ ConfigMap Creation
✅ Environment Variables from ConfigMap
✅ Volume Mounts
✅ emptyDir Volume
✅ Shell Commands in Containers
✅ Logging to Mounted Volumes
✅ Configuration Management in Kubernetes

💡 **Most Important Interview Point:**
Kubernetes promotes separating **application code**, **configuration**, and **storage**. ConfigMaps manage configuration, Volumes manage storage, and Pods run the application. This separation is a core Kubernetes design principle and is frequently discussed in DevOps interviews. 🚀
