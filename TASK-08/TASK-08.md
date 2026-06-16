# 🚀 Kubernetes Level-1 | Task-08 | Create a CronJob

## 🎯 Objective

Create a Kubernetes CronJob with the following specifications:

| Parameter      | Value                          |
| -------------- | ------------------------------ |
| CronJob Name   | `nautilus`                     |
| Schedule       | `*/9 * * * *`                  |
| Container Name | `cron-nautilus`                |
| Image          | `nginx:latest`                 |
| Command        | `echo Welcome to xfusioncorp!` |
| Restart Policy | `OnFailure`                    |

---

## 🏗️ Architecture

```text
CronJob (nautilus)
       │
       │ Every 9 Minutes
       ▼
      Job
       │
       ▼
      Pod
       │
       ▼
Container
cron-nautilus
nginx:latest

Command:
echo Welcome to xfusioncorp!
```

---

# 📝 Solution

## cronjob.yaml

```yaml
apiVersion: batch/v1
kind: CronJob

metadata:
  name: nautilus

spec:
  schedule: "*/9 * * * *"

  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-nautilus
            image: nginx:latest
            command:
            - /bin/sh
            - -c
            - echo Welcome to xfusioncorp!

          restartPolicy: OnFailure
```

---

## 🚀 Deploy CronJob

```bash
kubectl apply -f cronjob.yaml
```

Expected:

```text
cronjob.batch/nautilus created
```

---

# ✅ Verification Commands

### 1. Check CronJobs

```bash
kubectl get cronjobs
```

or

```bash
kubectl get cj
```

Expected:

```text
NAME       SCHEDULE      SUSPEND ACTIVE LAST SCHEDULE AGE
nautilus   */9 * * * *   False   0      <none>       10s
```

---

### 2. Describe CronJob

```bash
kubectl describe cronjob nautilus
```

Verify:

* Schedule
* Container Name
* Image
* Restart Policy
* Command

---

### 3. Check Jobs Created

```bash
kubectl get jobs
```

Example:

```text
NAME                 COMPLETIONS DURATION AGE
nautilus-28493783    1/1         4s       1m
```

---

### 4. Check Pods Created by Jobs

```bash
kubectl get pods
```

Example:

```text
NAME                          STATUS
nautilus-28493783-abcde       Completed
```

---

### 5. View Pod Logs

```bash
kubectl logs <pod-name>
```

Example:

```text
Welcome to xfusioncorp!
```

---

# 📚 Key Concepts Learned

## ⏰ What is a CronJob?

A CronJob creates Jobs on a repeating schedule.

Example:

```text
Every 5 Minutes
Every Hour
Every Day at Midnight
Every Sunday
```

CronJob is Kubernetes' equivalent of:

```text
Linux Cron
```

---

## Workflow

```text
CronJob
    │
Scheduled Time Arrives
    │
    ▼
Job Created
    │
    ▼
Pod Created
    │
    ▼
Command Executed
```

---

## Understanding the Schedule

Task:

```yaml
schedule: "*/9 * * * *"
```

Meaning:

```text
Every 9 Minutes
```

Cron Format:

```text
* * * * *
│ │ │ │ │
│ │ │ │ └── Day of Week
│ │ │ └──── Month
│ │ └────── Day of Month
│ └──────── Hour
└────────── Minute
```

Examples:

| Schedule      | Meaning           |
| ------------- | ----------------- |
| `*/5 * * * *` | Every 5 minutes   |
| `0 * * * *`   | Every hour        |
| `0 0 * * *`   | Midnight daily    |
| `0 9 * * 1`   | Every Monday 9 AM |

---

## 🧩 CronJob → Job → Pod

Architecture:

```text
CronJob
    │
    ▼
Job
    │
    ▼
Pod
    │
    ▼
Container
```

CronJob never creates Pods directly.

It creates:

1. Job
2. Job creates Pod
3. Pod runs command

---

## 🔄 Restart Policy

Task Requirement:

```yaml
restartPolicy: OnFailure
```

Meaning:

If:

```text
Command Fails
```

Pod restarts.

If:

```text
Command Succeeds
```

Pod exits successfully.

---

## 🐳 Command Execution

Task:

```yaml
command:
- /bin/sh
- -c
- echo Welcome to xfusioncorp!
```

Equivalent Linux command:

```bash
/bin/sh -c "echo Welcome to xfusioncorp!"
```

Output:

```text
Welcome to xfusioncorp!
```

---

# 🛠️ Useful Commands

### Create CronJob

```bash
kubectl apply -f cronjob.yaml
```

### List CronJobs

```bash
kubectl get cronjobs
```

Short form:

```bash
kubectl get cj
```

### Describe CronJob

```bash
kubectl describe cronjob nautilus
```

### Check Jobs

```bash
kubectl get jobs
```

### Check Pods

```bash
kubectl get pods
```

### View Logs

```bash
kubectl logs <pod-name>
```

### Delete CronJob

```bash
kubectl delete cronjob nautilus
```

---

# 🎤 Interview Explanation

> "In this task, I created a CronJob named `nautilus` that executes on a recurring schedule. The CronJob uses the `nginx:latest` image and runs the command `echo Welcome to xfusioncorp!`. Kubernetes CronJobs are similar to Linux cron jobs and are used to automate recurring tasks such as backups, report generation, cleanup jobs, and health checks. Internally, a CronJob creates a Job, and the Job creates a Pod to execute the task."

---

# 🔥 Interview Questions

### 1️⃣ What is a CronJob in Kubernetes?

A CronJob creates Jobs periodically based on a cron schedule.

---

### 2️⃣ What is the relationship between CronJob, Job, and Pod?

```text
CronJob
    │
    ▼
Job
    │
    ▼
Pod
```

CronJob schedules Jobs.

Jobs create Pods.

---

### 3️⃣ What does this schedule mean?

```yaml
*/9 * * * *
```

Answer:

```text
Run every 9 minutes
```

---

### 4️⃣ What is the Cron format?

```text
* * * * *
│ │ │ │ │
│ │ │ │ └─ Day of Week
│ │ │ └── Month
│ │ └──── Day of Month
│ └────── Hour
└──────── Minute
```

---

### 5️⃣ What is `restartPolicy: OnFailure`?

If the Pod fails:

```text
Restart the Pod
```

If the Pod succeeds:

```text
Do not restart
```

---

### 6️⃣ Difference Between Job and CronJob?

| Job              | CronJob             |
| ---------------- | ------------------- |
| Runs once        | Runs repeatedly     |
| Manual Execution | Scheduled Execution |
| Single Task      | Recurring Task      |

---

### 7️⃣ Can CronJob create Pods directly?

❌ No

Workflow:

```text
CronJob
   ↓
Job
   ↓
Pod
```

---

### 8️⃣ Common CronJob Use Cases?

✅ Database Backups
✅ Log Cleanup
✅ Report Generation
✅ Health Checks
✅ Email Notifications
✅ Data Synchronization

---

### 9️⃣ How do you suspend a CronJob?

```bash
kubectl patch cronjob nautilus \
-p '{"spec":{"suspend":true}}'
```

---

### 🔟 How do you check CronJob execution history?

```bash
kubectl get jobs
kubectl get pods
kubectl logs <pod-name>
```

---

# 📌 Task Summary (Interview Revision)

This task introduces **Kubernetes Scheduled Workloads**.

### Key Learnings

✅ CronJob Creation
✅ Cron Schedule Syntax
✅ CronJob → Job → Pod Workflow
✅ Command Execution
✅ Restart Policies
✅ Viewing Jobs and Logs
✅ Scheduled Automation

💡 **Most Important Interview Point:**
A CronJob does not create Pods directly. It creates a **Job**, and the Job creates a **Pod** to execute the scheduled task. This hierarchy is a very common Kubernetes interview question. 🚀
