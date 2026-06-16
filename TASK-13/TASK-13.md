# 🚀 Kubernetes Level-1 | Task-13 | Create NodePort Service for Existing ReplicaSet

## 🎯 Objective

Expose the existing ReplicaSet Pods through a NodePort Service.

### Requirements

* Existing ReplicaSet: `nginx-replicaset`
* Pod Labels:

  * `app=nginx_app`
  * `type=front-end`
* Service Name: `nginx-service`
* Service Type: `NodePort`
* NodePort: `30080`
* Service Port: `80`

---

## 📌 Step 1: Verify Existing ReplicaSet

```bash
kubectl get rs

kubectl describe rs nginx-replicaset
```

Output:

```text
NAME               DESIRED   CURRENT   READY
nginx-replicaset   3         3         3
```

Selector:

```text
app=nginx_app,type=front-end
```

---

## 📌 Step 2: Verify Pods

```bash
kubectl get pods

kubectl get pods -o wide
```

Output:

```text
nginx-replicaset-f2x4v
nginx-replicaset-kskgj
nginx-replicaset-xmbbd
```

---

## 📌 Step 3: Create NodePort Service YAML

### nodeport.yaml

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  type: NodePort

  selector:
    app: nginx_app

  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30080
```

---

## 📌 Step 4: Apply the YAML

```bash
kubectl apply -f nodeport.yaml
```

Output:

```text
service/nginx-service created
```

---

## 📌 Step 5: Verify Service

```bash
kubectl get svc
```

Output:

```text
NAME            TYPE       PORT(S)

nginx-service   NodePort   80:30080/TCP
```

---

## 📌 Step 6: Verify Service Details

```bash
kubectl get svc -o wide
```

Output:

```text
NAME            TYPE       CLUSTER-IP    PORT(S)

nginx-service   NodePort   10.43.7.15    80:30080/TCP

SELECTOR

app=nginx_app
```

---

## 📌 Step 7: Verify ClusterIP Access

```bash
curl http://10.43.7.15:80
```

Expected:

```text
Welcome to nginx!
```

---

## 📌 Step 8: Verify NodePort Access

Find Node IP:

```bash
kubectl get nodes -o wide
```

Output:

```text
INTERNAL-IP

10.244.81.49
```

Access:

```bash
curl http://10.244.81.49:30080
```

Expected:

```text
Welcome to nginx!
```

---

# 🏗️ Architecture

```
             Node IP

      10.244.81.49:30080

                 │

                 ▼

        nginx-service

         NodePort

           30080

                 │

         Selector

      app=nginx_app

                 │

  ┌────────┬────────┬────────┐
  ▼        ▼        ▼
```

nginx-rs   nginx-rs   nginx-rs

```
 Pod        Pod        Pod
```

nginx      nginx      nginx

---

# ⚠️ Issues Faced During the Lab

### Issue 1

Created a Deployment instead of exposing the existing ReplicaSet.

❌ Wrong:

```bash
kubectl create deployment nginx-deployment
```

✅ Correct:

Create a Service targeting:

```text
app=nginx_app
```

---

### Issue 2

YAML Indentation Error

Error:

```text
yaml: line 11:
did not find expected '-' indicator
```

Cause:

Incorrect indentation under:

```yaml
ports:
```

Fix:

```yaml
ports:
- protocol: TCP
  port: 80
  targetPort: 80
  nodePort: 30080
```

---

### Issue 3

Incorrect curl port

❌

```bash
curl http://10.22.0.10:89
```

✅

```bash
curl http://10.22.0.10:80
```

---

# 🎤 Interview Questions

### 1. What is a NodePort Service?

A NodePort Service exposes Pods externally using:

```text
<NodeIP>:<NodePort>
```

Example:

```text
10.244.81.49:30080
```

---

### 2. Default NodePort Range?

```text
30000 - 32767
```

---

### 3. Difference between ClusterIP and NodePort?

| ClusterIP            | NodePort                   |
| -------------------- | -------------------------- |
| Internal Access      | External Access            |
| Default Service Type | Exposes Node IP + Port     |
| Cluster Only         | Accessible Outside Cluster |

---

### 4. How does a Service find Pods?

Using:

```yaml
selector:
  app: nginx_app
```

which matches:

```yaml
labels:
  app: nginx_app
```

---

### 5. How do you verify Service connectivity?

```bash
kubectl get svc

kubectl get endpoints nginx-service

curl http://<NodeIP>:<NodePort>
```

---

# ✅ Key Learnings

* ReplicaSet manages Pods.
* Services expose Pods.
* NodePort provides external access.
* Labels and Selectors must match exactly.
* YAML indentation is critical.
* Always verify using:

```bash
kubectl get svc

kubectl get pods --show-labels

kubectl get endpoints

curl http://<NodeIP>:<NodePort>
```
