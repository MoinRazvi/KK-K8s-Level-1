# 🚀 Kubernetes Level-1 | Task-09 | Create a Job

## 🎯 Objective

Create a Kubernetes Job with the following requirements:

| Parameter         | Value                        |
| ----------------- | ---------------------------- |
| Job Name          | `countdown-devops`           |
| Pod Template Name | `countdown-devops`           |
| Container Name    | `container-countdown-devops` |
| Image             | `ubuntu:latest`              |
| Command           | `sleep 5`                    |
| Restart Policy    | `Never`                      |

---

## 🏗️ Architecture

```text
Job (countdown-devops)
        │
        ▼
Pod Template
(metadata.name = countdown-devops)
        │
        ▼
Pod
        │
        ▼
Container
container-countdown-devops
ubuntu:latest

Command:
sleep 5
```

---

# 📝 Solution

## job.yaml

```yaml
apiVersion: batch/v1
kind: Job

metadata:
  name: countdown-devops

spec:
  template:

    metadata:
      name: countdown-devops

    spec:
      containers:
      - name: container-countdown-devops
        image: ubuntu:latest
        command:
        - sleep
        - "5"

      restartPolicy: Never
```

---

## 🚀 Deploy Job

```bash
kubectl apply -f job.yaml
```

Expected:

```text
job.batch/countdown-devops created
```

---

# ✅ Verification Commands

### Check Jobs

```bash
kubectl get jobs
```

Expected:

```text
NAME                COMPLETIONS   DURATION   AGE
countdown-devops    1/1           5s         10s
```

---

### Describe Job

```bash
kubectl describe job countdown-devops
```

Verify:

* Job Name
* Pod Template Name
* Container Name
* Image
* Restart Policy
* Command

---

### Check Pods Created by Job

```bash
kubectl get pods
```

Example:

```text
NAME                               READY   STATUS
countdown-devops-abcde             0/1     Completed
```

---

### View Pod Details

```bash
kubectl describe pod <pod-name>
```

---

### Export YAML

```bash
kubectl get job countdown-devops -o yaml
```

---

# 📚 Key Concepts Learned

## 🏃 What is a Job?

A Job runs a task **once** and ensures it completes successfully.

Examples:

✅ Database Migration
✅ Data Import
✅ Backup Script
✅ Batch Processing
✅ One-Time Initialization

---

## Workflow

```text
Job
 │
 ▼
Creates Pod
 │
 ▼
Runs Command
 │
 ▼
Command Completes
 │
 ▼
Job Status = Completed
```

---

## Job vs Pod

| Pod                      | Job                       |
| ------------------------ | ------------------------- |
| Runs Containers          | Runs Tasks                |
| May run forever          | Finishes after completion |
| No completion tracking   | Tracks completion         |
| Not ideal for batch work | Designed for batch work   |

---

## Job vs CronJob

| Job            | CronJob             |
| -------------- | ------------------- |
| Executes Once  | Executes Repeatedly |
| Manual Trigger | Scheduled Trigger   |
| One-Time Task  | Recurring Task      |

---

## Why `restartPolicy: Never`?

Task Requirement:

```yaml
restartPolicy: Never
```

Meaning:

```text
If Container Fails

Do Not Restart
```

Allowed values for Jobs:

```text
Never
OnFailure
```

❌ Not Allowed:

```text
Always
```

---

## Command Section

Task:

```yaml
command:
- sleep
- "5"
```

Equivalent Linux command:

```bash
sleep 5
```

Container:

```text
Sleeps for 5 seconds
Exits Successfully
```

Job Status:

```text
Completed
```

---

# 🛠️ Useful Commands

### Create Job

```bash
kubectl apply -f job.yaml
```

### List Jobs

```bash
kubectl get jobs
```

### Describe Job

```bash
kubectl describe job countdown-devops
```

### View Pods Created by Job

```bash
kubectl get pods
```

### View Logs

```bash
kubectl logs <pod-name>
```

### Delete Job

```bash
kubectl delete job countdown-devops
```

---

# 🎤 Interview Explanation

> "In this task, I created a Kubernetes Job named `countdown-devops`. The Job uses the `ubuntu:latest` image and executes the command `sleep 5`. Jobs are designed for finite tasks that need to run to completion. Kubernetes creates a Pod for the Job, monitors its execution, and marks the Job as Completed once the command exits successfully."

---

# 🔥 Interview Questions

### 1️⃣ What is a Job in Kubernetes?

A Job is a Kubernetes resource that runs one or more Pods until a specified task completes successfully.

---

### 2️⃣ What is the difference between Job and CronJob?

| Job              | CronJob             |
| ---------------- | ------------------- |
| Runs Once        | Runs on Schedule    |
| Manual Execution | Automated Execution |
| Batch Task       | Recurring Task      |

---

### 3️⃣ What happens when a Job completes?

```text
Pod Status → Completed

Job Status → Complete
```

The Pod is not restarted.

---

### 4️⃣ What restart policies are allowed for Jobs?

✅ `Never`

✅ `OnFailure`

❌ `Always`

---

### 5️⃣ What is the relationship between Job and Pod?

```text
Job
 │
 ▼
Pod
 │
 ▼
Container
```

A Job creates and manages Pods.

---

### 6️⃣ What happens if the Pod fails?

If:

```yaml
restartPolicy: OnFailure
```

The container restarts.

If:

```yaml
restartPolicy: Never
```

The Pod fails and Job may create another Pod based on retry policy.

---

### 7️⃣ Can a Job run multiple Pods?

✅ Yes

Using:

```yaml
completions:
parallelism:
```

Example:

```yaml
completions: 5
parallelism: 2
```

---

### 8️⃣ What is the difference between Deployment and Job?

| Deployment               | Job                        |
| ------------------------ | -------------------------- |
| Long Running Application | Finite Task                |
| Pods run continuously    | Pods stop after completion |
| Web Applications         | Batch Processing           |
| Supports Rolling Updates | Tracks Completion          |

---

### 9️⃣ How do you check Job status?

```bash
kubectl get jobs
```

or

```bash
kubectl describe job countdown-devops
```

---

### 🔟 Why use a Job instead of a Pod?

Because Jobs provide:

* Completion Tracking
* Retry Mechanism
* Failure Handling
* Batch Execution

Pods alone do not provide these features.

---

# 📌 Task Summary (Interview Revision)

This task introduces **Batch Workloads** in Kubernetes.

### Key Learnings

✅ Job Creation
✅ Job → Pod → Container Workflow
✅ One-Time Task Execution
✅ Restart Policies (`Never`, `OnFailure`)
✅ Command Execution
✅ Completion Tracking
✅ Job vs CronJob
✅ Job vs Deployment

💡 **Most Important Interview Point:**
A Job is designed for **finite tasks**. Unlike Deployments that keep applications running continuously, Jobs ensure that a task runs successfully **to completion** and then exits. This distinction is one of the most commonly asked Kubernetes interview topics. 🚀
