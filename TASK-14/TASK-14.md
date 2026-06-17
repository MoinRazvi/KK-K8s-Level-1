# 🚀 Kubernetes Level-1 | Task-14 | Troubleshoot Nginx + PHP-FPM Pod with ConfigMap and Shared Volume

## 🎯 Objective

Troubleshoot and fix the `nginx-phpfpm` Pod and the `nginx-config` ConfigMap.

After fixing the issue:

* Copy `/home/thor/index.php` from jump host.
* Place it in the correct Nginx document root.
* Verify the website using the **Website** button.

---

# 🏗️ Architecture

```
                    nginx-phpfpm Pod

         ┌──────────────────────────────────┐

         │        shared-files Volume       │
         │         (emptyDir Volume)        │

         └──────────────────────────────────┘

             ▲                         ▲

             │                         │

     php-fpm-container          nginx-container

     Mount Path                 Mount Path

     /var/www/html              /var/www/html


             ▲

             │

      nginx-config ConfigMap

             │

             ▼

     /etc/nginx/nginx.conf
```

---

# 📌 Step 1: Check Pod Status

```bash
kubectl get pods
```

Output:

```text
nginx-phpfpm   2/2   Running
```

---

# 📌 Step 2: Describe Pod

```bash
kubectl describe pod nginx-phpfpm
```

Important Observations:

### Containers

```text
php-fpm-container

nginx-container
```

### Shared Volume

```text
shared-files
Type: EmptyDir
```

### ConfigMap Volume

```text
nginx-config-volume

ConfigMap: nginx-config
```

---

# 📌 Step 3: Check ConfigMap

```bash
kubectl get cm nginx-config -o yaml
```

Config:

```nginx
root /var/www/html;

index index.html index.htm index.php;

fastcgi_pass 127.0.0.1:9000;
```

---

# 📌 Step 4: Check Existing Files

```bash
kubectl exec -it nginx-phpfpm \
-c nginx-container -- ls -l /var/www/html
```

Output:

```text
total 0
```

No files existed.

---

# ❌ Initial Wrong Assumption

At first, I suspected:

```text
listen 8099
```

was causing the issue.

However:

✅ Website button was already configured.

❌ Port was NOT the problem.

---

# 🔍 Root Cause

The issue was the **document root path**.

The Pod used:

```text
/var/www/html
```

for both containers.

The file had to be copied exactly there.

---

# ❌ Incorrect Command

```bash
kubectl cp /home/thor/index.php \
nginx-phpfpm:/var/www/html/index.php \
-c nginx-config
```

Error:

```text
container nginx-config is not valid
```

Reason:

`nginx-config` is a ConfigMap.

It is NOT a container.

---

# ✅ Correct Command

```bash
kubectl cp /home/thor/index.php \
nginx-phpfpm:/var/www/html/index.php \
-c nginx-container
```

---

# 📌 Verify File

```bash
kubectl exec -it nginx-phpfpm \
-c nginx-container \
-- ls -l /var/www/html
```

Output:

```text
index.php
```

---

# 📌 Verify Content

```bash
kubectl exec -it nginx-phpfpm \
-c nginx-container \
-- cat /var/www/html/index.php
```

Output:

```php
<?php
phpinfo();
?>
```

---

# 📌 Verify Pod YAML

```bash
kubectl get pod nginx-phpfpm -o yaml > pod.yaml
```

Important Mounts:

### PHP Container

```yaml
volumeMounts:
- mountPath: /var/www/html
  name: shared-files
```

### Nginx Container

```yaml
volumeMounts:
- mountPath: /var/www/html
  name: shared-files

- mountPath: /etc/nginx/nginx.conf
  name: nginx-config-volume
  subPath: nginx.conf
```

---

# 📌 Backup ConfigMap

```bash
kubectl get cm nginx-config -o yaml > cm.yaml
```

---

# ⚠️ Important Learning

I accidentally deleted the Pod:

```bash
kubectl delete pod nginx-phpfpm
```

Result:

```text
No resources found
```

Reason:

The Pod was a:

```text
Standalone Pod
```

and not managed by:

* Deployment ❌
* ReplicaSet ❌
* StatefulSet ❌

Therefore Kubernetes did not recreate it.

---

# 🧠 Troubleshooting Flow

```text
Website Not Working

        │

        ▼

Check Pod Status

        │

        ▼

Check ConfigMap

        │

        ▼

Check Mount Paths

        │

        ▼

Check Shared Volume

        │

        ▼

Verify index.php

        │

        ▼

Copy File to Correct Path

        │

        ▼

Website Working ✅
```

---

# 🎤 Interview Questions

### 1. What is EmptyDir Volume?

An EmptyDir Volume:

* Created when Pod starts
* Shared among containers
* Deleted when Pod is deleted

Example:

```yaml
volumes:
- emptyDir: {}
  name: shared-files
```

---

### 2. What is ConfigMap?

A ConfigMap stores:

* Application Configuration
* nginx.conf
* Environment Variables

Example:

```yaml
volumes:
- configMap:
    name: nginx-config
```

---

### 3. Difference between EmptyDir and ConfigMap?

| EmptyDir                  | ConfigMap            |
| ------------------------- | -------------------- |
| Stores Runtime Data       | Stores Configuration |
| Writable                  | Read-only            |
| Shared Between Containers | Mounted as Files     |
| Deleted with Pod          | Independent Object   |

---

### 4. Why did the Pod not recreate automatically?

Because:

```text
nginx-phpfpm
```

was a Standalone Pod.

Only these recreate Pods automatically:

* Deployment
* ReplicaSet
* StatefulSet
* DaemonSet

---

### 5. How do you copy files to a Container?

```bash
kubectl cp local-file \
pod-name:/path \
-c container-name
```

Example:

```bash
kubectl cp /home/thor/index.php \
nginx-phpfpm:/var/www/html/index.php \
-c nginx-container
```

---

# ✅ Key Takeaways

* Always verify mount paths before copying files.
* ConfigMap is not a container.
* EmptyDir enables file sharing between containers.
* Standalone Pods are not recreated automatically.
* Backup YAML before deleting a Pod.
* Website issues are often caused by incorrect document root or missing files, not only by ports.

🚀 **The real DevOps skill is not creating resources — it's systematically troubleshooting and identifying the actual root cause.**
