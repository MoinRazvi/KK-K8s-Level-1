
#
### thor@jump-host ~$ kubectl create configmap time-config \
--from-literal=TIME_FREQ=11 \
-n xfusion
error: failed to create configmap: namespaces "xfusion" not found - seems there is no namespace xfusion have to create the name space as well.
#
### (means the namespace does not exist in your lab environment, so you need to create it first.)

The error:

```text
namespaces "xfusion" not found
```

means the namespace **does not exist** in your lab environment, so you need to create it first.

---

## Step 1: Create Namespace

```bash
kubectl create namespace xfusion
```

Verify:

```bash
kubectl get ns
```

Expected:

```text
NAME              STATUS   AGE
default           Active
kube-system       Active
xfusion           Active
```

---

## Step 2: Create ConfigMap

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

## Step 3: Create Pod YAML

Save as `time-check.yaml`

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

Apply:

```bash
kubectl apply -f time-check.yaml
```

---

## Step 4: Verify Everything

### Pod

```bash
kubectl get pods -n xfusion
```

---

### ConfigMap

```bash
kubectl get cm -n xfusion
```

---

### Check Environment Variable

```bash
kubectl exec -it time-check -n xfusion -- env | grep TIME_FREQ
```

Expected:

```text
TIME_FREQ=11
```

---

### Check Log File

```bash
kubectl exec -it time-check -n xfusion -- cat /opt/data/time/time-check.log
```

Expected:

```text
Tue Jun 16 12:10:01 UTC 2026
Tue Jun 16 12:10:12 UTC 2026
Tue Jun 16 12:10:23 UTC 2026
```

---

## ⚠️ One Important KodeKloud Note

For this particular task, the validator usually checks:

* Namespace = `xfusion`
* ConfigMap name = `time-config`
* Key = `TIME_FREQ`
* Pod name = `time-check`
* Container name = `time-check`
* Volume name = `log-volume`
* Mount path = `/opt/data/time`
* Log file = `/opt/data/time/time-check.log`

So **be very careful with spelling**, especially:

```text
TIME_FREQ      ✅
time_freq      ❌

log-volume     ✅
logvolume      ❌

time-check     ✅
timecheck      ❌
```

A single character mismatch can cause the lab validation to fail even if the Pod is running successfully. 🚀
