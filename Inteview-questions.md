# 🧠 Important Kubernetes Concepts Learned

### Workloads

* Pod
* ReplicaSet
* Deployment
* Job
* CronJob

---

### Networking

* ClusterIP
* NodePort
* Service Selectors

---

### Storage

* EmptyDir
* ConfigMap Volume

---

### Configuration

* ConfigMap
* Environment Variables

---

### Troubleshooting

* ImagePullBackOff
* CrashLoopBackOff
* Resource Limits
* Volume Mount Issues
* ConfigMap Issues
* Service Connectivity Issues

---

# 🎤 Most Important Interview Questions

### 1. Difference between Pod and Deployment?

| Pod              | Deployment        |
| ---------------- | ----------------- |
| Single Instance  | Manages Pods      |
| Not Self Healing | Self Healing      |
| Manual Scaling   | Automatic Scaling |

---

### 2. Difference between Deployment and ReplicaSet?

* ReplicaSet maintains replica count.
* Deployment manages ReplicaSets and provides rolling updates.

---

### 3. Difference between ClusterIP and NodePort?

| ClusterIP       | NodePort          |
| --------------- | ----------------- |
| Internal Access | External Access   |
| Default Service | Exposes Node Port |

---

### 4. What is ConfigMap?

Stores:

* Configuration Files
* Environment Variables
* Application Settings

---

### 5. What happens if a Standalone Pod is deleted?

It is deleted permanently.

Kubernetes recreates Pods only when managed by:

* Deployment
* ReplicaSet
* StatefulSet
* DaemonSet

---

