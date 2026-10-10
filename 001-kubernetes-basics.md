# ☸️ Kubernetes Basics

A practical reference guide for learning Kubernetes from scratch — covering cluster architecture, Pods and their controllers, networking, configuration, storage, and day-to-day `kubectl` operations.

---

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
  - [From Monolith to Microservices](#from-monolith-to-microservices)
  - [Why Docker?](#why-docker)
- [What Is Kubernetes?](#️-what-is-kubernetes)
  - [History](#history)
- [Kubernetes Architecture](#️-kubernetes-architecture)
  - [Kubernetes Master](#kubernetes-master)
  - [Kubernetes Nodes](#kubernetes-nodes)
  - [What Happens When You Deploy](#what-happens-when-you-deploy)
- [Installation](#️-installation)
  - [Installing Kubernetes Locally](#installing-kubernetes-locally)
  - [Installing kubectl](#installing-kubectl)
- [Node](#️-node)
  - [Node Commands](#node-commands)
- [Pod](#-pod)
  - [Pod Template](#pod-template)
  - [Pod Commands](#pod-commands)
- [Labels](#️-labels)
  - [Pod with Labels](#pod-with-labels)
  - [Label Commands](#label-commands)
- [Annotations](#-annotations)
  - [Pod with Annotations](#pod-with-annotations)
  - [Annotation Commands](#annotation-commands)
  - [Labels vs Annotations](#labels-vs-annotations)
- [Namespace](#-namespace)
  - [Namespace Template](#namespace-template)
  - [Namespace Commands](#namespace-commands)
- [Probe](#-probe)
  - [Probe Types](#probe-types)
  - [Probe Mechanisms](#probe-mechanisms)
  - [Pod with Probes](#pod-with-probes)
  - [Probe Configuration Fields](#probe-configuration-fields)
- [Replication Controller](#-replication-controller)
  - [Replication Controller Template](#replication-controller-template)
  - [Replication Controller Commands](#replication-controller-commands)
- [Replica Set](#-replica-set)
  - [Replica Set Template](#replica-set-template)
  - [Replica Set with matchExpressions](#replica-set-with-matchexpressions)
  - [Match Expression Operators](#match-expression-operators)
  - [Replica Set Commands](#replica-set-commands)
  - [Replication Controller vs Replica Set](#replication-controller-vs-replica-set)
- [Daemon Set](#-daemon-set)
  - [Daemon Set Template](#daemon-set-template)
  - [Daemon Set Commands](#daemon-set-commands)
- [Job](#️-job)
  - [Job Template](#job-template)
  - [Job Commands](#job-commands)
- [Cron Job](#-cron-job)
  - [Cron Job Template](#cron-job-template)
  - [Cron Job Commands](#cron-job-commands)
- [Node Selector](#-node-selector)
  - [Pod with nodeSelector](#pod-with-nodeselector)
  - [Replica Set with nodeSelector](#replica-set-with-nodeselector)
- [Service](#-service)
  - [Service Types](#service-types)
  - [ClusterIP Service](#clusterip-service)
  - [NodePort Service](#nodeport-service)
  - [LoadBalancer Service](#loadbalancer-service)
  - [Service Commands](#service-commands)
  - [Accessing a Service from Inside a Pod](#accessing-a-service-from-inside-a-pod)
- [External Service](#-external-service)
  - [External Service with Manual Endpoints](#external-service-with-manual-endpoints)
  - [External Service with ExternalName](#external-service-with-externalname)
- [Ingress](#-ingress)
  - [Ingress Template](#ingress-template)
  - [Ingress Commands](#ingress-commands)
- [Multi Container Pod](#-multi-container-pod)
  - [Multi Container Pod with Service](#multi-container-pod-with-service)
- [Volume](#-volume)
  - [Volume Types](#volume-types)
  - [Pod with emptyDir Volume](#pod-with-emptydir-volume)
  - [Sharing a Volume Between Containers](#sharing-a-volume-between-containers)
- [Environment Variable](#-environment-variable)
  - [Pod with Environment Variables](#pod-with-environment-variables)
- [ConfigMap](#️-configmap)
  - [ConfigMap Template](#configmap-template)
  - [Using a ConfigMap in a Pod](#using-a-configmap-in-a-pod)
  - [ConfigMap Commands](#configmap-commands)
- [Secret](#-secret)
  - [Secret Template](#secret-template)
  - [Using ConfigMap and Secret Together](#using-configmap-and-secret-together)
  - [Secret Commands](#secret-commands)
  - [ConfigMap vs Secret](#configmap-vs-secret)
- [Downward API](#-downward-api)
  - [Pod with Downward API](#pod-with-downward-api)
  - [Available Fields](#available-fields)
- [Managing Kubernetes Objects](#️-managing-kubernetes-objects)
  - [Create vs Apply vs Replace vs Delete](#create-vs-apply-vs-replace-vs-delete)
  - [Object Management Commands](#object-management-commands)
- [StatefulSet](#-statefulset)
  - [StatefulSet Template](#statefulset-template)
  - [StatefulSet Commands](#statefulset-commands)
  - [Replica Set vs StatefulSet](#replica-set-vs-statefulset)
- [Computational Resources](#-computational-resources)
  - [Request vs Limit](#request-vs-limit)
  - [Pod with Resource Requests and Limits](#pod-with-resource-requests-and-limits)
  - [Resource Units](#resource-units)
- [Horizontal Pod Autoscaler](#-horizontal-pod-autoscaler)
  - [Vertical vs Horizontal Scaling](#vertical-vs-horizontal-scaling)
  - [HPA Template](#hpa-template)
  - [HPA Commands](#hpa-commands)
- [Quick Reference](#-quick-reference)
  - [Resources](#resources)
  - [kubectl Commands](#kubectl-commands)
- [Best Practices](#-best-practices)

---

## 🧩 Prerequisites

### From Monolith to Microservices

> **Analogy:** Think of a traditional restaurant with one big kitchen handling everything — cooking rice, frying chicken, making drinks — all in the same place. If the stove breaks, the entire restaurant shuts down. A modern restaurant separates concerns: one booth for drinks, one for main courses, one for desserts. If the drinks booth has an issue, food service keeps running. That's the difference between a monolith and microservices.

- **Monolith** — An application where all features are built and bundled together in a single unit
- **Microservices** — The opposite of a monolith; the application is broken into small, focused services where each handles one task well and they communicate with each other

### Why Docker?

> **Analogy:** Docker is like a lunchbox. You pack your meal together with all the ingredients and utensils inside the box. No matter whose kitchen you open it in, the food comes out exactly the same — you never have to worry about "it works on my machine but not on yours."

- Kubernetes runs **containers**, so every application must first be packaged as a container image
- Kubernetes supports several container runtimes
- Docker is currently the most popular and widely used tool for building those images

---

## ☸️ What Is Kubernetes?

> **Analogy:** Kubernetes is like the operations manager of a large franchise chain. You have hundreds of branches (servers), each with their own staff (applications). Without a manager, you'd have to personally visit every branch to make sure they're open, properly staffed, and not overwhelmed. Kubernetes does all of this automatically — opening new branches when demand spikes, closing idle ones, and relocating staff when a building suddenly goes offline.

- **Definition** — Kubernetes is an application for automating the deployment, scaling, and management of container-based applications
- **Open Source** — Kubernetes is open source and currently the most popular container orchestration system
- **Widely Adopted** — Many large companies, including unicorns in Indonesia, already use Kubernetes

> Reference: [kubernetes.io](https://kubernetes.io/)

### History

- Google ran an internal system called **Borg**, followed later by **Omega**, to help developers and infra engineers manage thousands of servers
- In 2014, Google introduced **Kubernetes** as an open source system built on the experience from Borg, Omega, and other internal systems

---

## 🏗️ Kubernetes Architecture

> **Analogy:** Think of Kubernetes as a large package delivery company. **You (the developer)** are a customer who wants to ship many packages to different cities, and **Kubernetes** is the logistics company that handles everything. The **Master** is the head office; the **Worker Nodes** are the warehouses and drivers in the field.

### Kubernetes Master

The Master (also called the **control plane**) makes every decision about the cluster.

| Component | Role | Analogy |
| --- | --- | --- |
| `kube-apiserver` | The API used to interact with the Kubernetes Cluster | **The front desk receptionist** — every instruction goes through it, and all other components only talk to each other through it, never directly |
| `etcd` | The database that stores Kubernetes Cluster data | **The official record book** — the single source of truth ("Package #A023 is on its way to City B"); if the head office restarts, everything is restored from this book |
| `kube-scheduler` | Watches for new Pods and assigns them to Nodes | **The dispatcher** — one job only: "Which warehouse has spare capacity for this package?" Nothing more, nothing less |
| `kube-controller-manager` | Controls the overall state of the Kubernetes Cluster | **The operations manager** — you asked for 3 Pods but only 2 are alive, so it spins up another; this non-stop loop is the heart of self-healing |
| `cloud-controller-manager` | Manages interaction with the cloud provider | **The vendor liaison** — requests load balancers and storage from AWS, GCP, or Azure; unused when running on-premise |

### Kubernetes Nodes

| Component | Role | Analogy |
| --- | --- | --- |
| `kubelet` | Runs on every Node and ensures applications are running | **The warehouse supervisor** — receives "run `nginx:latest` here" from head office, executes it, and reports back "Done / Failed" |
| `kube-proxy` | Runs on every Node; acts as a network proxy and load balancer | **The traffic director** — routes a request for "Service A" to the right Pod |
| Container runtime | Runs on every Node and manages containers (containerd, cri-o, Docker, etc.) | **The driver and truck** — the component that actually runs the container at the OS level |

> **Note:** Since Kubernetes 1.24, Docker Engine is no longer a built-in runtime — most clusters use **containerd** directly. Images built with Docker still run unchanged, because they follow the same OCI image standard.

### What Happens When You Deploy

Knowing which component does what explains most of the behaviour later in this guide:

```text
kubectl apply -f nginx.yaml            you describe the desired state
  ↓
kube-apiserver                         validates the request
  ↓
etcd                                   stores the desired state
  ↓
kube-controller-manager                notices "3 Pods wanted, 0 running" → creates Pod objects
  ↓
kube-scheduler                         picks a Node for each unscheduled Pod
  ↓
kubelet (on that Node)                 sees a Pod assigned to it
  ↓
Container runtime                      pulls the image and starts the container
  ↓
kubelet                                reports status back through kube-apiserver
```

> **Key Insight:** You never tell Kubernetes *how* to do something — you declare *what* you want, and the controllers keep working until reality matches it. That reconciliation loop is why a deleted Pod simply comes back.

---

## 🛠️ Installation

### Installing Kubernetes Locally

- **Docker Desktop** — Enable Kubernetes directly from the Docker Desktop settings
- **Minikube** — Requires VirtualBox or Hyper-V ([github.com/kubernetes/minikube](https://github.com/kubernetes/minikube))

### Installing kubectl

- Follow the official guide at `https://kubernetes.io/docs/tasks/tools/install-kubectl/`
- Verify the installation:

  ```bash
  kubectl version --client
  ```

---

## 🖥️ Node

> **Analogy:** A Node is like a warehouse building in a city. Inside one warehouse (node), there can be many workrooms (pods). Kubernetes can manage many warehouses across many cities — all coordinated from the head office.

- **Definition** — A Node is a worker machine in Kubernetes (also called a Minion); it can be a VM or a physical machine
- Every Node always runs `kubelet`, `kube-proxy`, and a container runtime

### Node Commands

```bash
kubectl get node                                # list all nodes
kubectl describe node <node-name>               # describe a specific node
kubectl label node <node-name> <key>=<value>    # add a label to a node
```

Example:

```bash
kubectl describe node orbstack
kubectl label node orbstack gpu=true
```

> **Tip:** Node labels are what [Node Selector](#-node-selector) matches against — label a Node first, then pin Pods to it.

---

## 📦 Pod

> **Analogy:** A Pod is like a single workroom inside a warehouse. The room can have one or more workers (containers) sharing the same desk and phone line. If the room is demolished, all workers inside go with it — but Kubernetes can immediately build a new identical room to replace it.

- **Definition** — A Pod is the smallest deployable unit in Kubernetes; it contains one or more containers
- **Purpose** — A Pod is essentially your application running inside the Kubernetes Cluster

### Pod Template

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-name
spec:
  containers:
    - name: container-name
      image: image-name
      ports:
        - containerPort: 80
```

### Pod Commands

```bash
kubectl get pod                     # list all pods
kubectl get pod -o wide             # includes IP and Node info
kubectl get pod -w                  # watch for changes
kubectl describe pod <pod-name>     # describe a pod
kubectl create -f nginx.yaml        # create a pod from a file
kubectl delete pod <pod-name>       # delete a pod
kubectl delete pod --all            # delete every pod in the namespace
kubectl exec -it <pod-name> -- /bin/sh                         # open a shell inside a pod
kubectl port-forward <pod-name> <host-port>:<container-port>   # forward a pod port to the host
```

Example:

```bash
kubectl port-forward nginx 8888:80
```

> **Gotcha:** A Pod created on its own is not self-healing — delete it and it's gone for good. Pods are almost always created through a controller such as a [Replica Set](#-replica-set), which recreates them when they die.

---

## 🏷️ Labels

> **Analogy:** Labels are like colored sticker tags on shipping boxes. A red sticker means "Finance team", a blue sticker means "Production environment", a yellow one means "Version 1.4.5." With these stickers, you can instantly find all Finance-team boxes that are still on version 1.x — without opening each box individually.

- **Purpose** — Labels are used to tag, organize, and add information to Pods and other resources
- Labels can be applied to any resource in Kubernetes (Pods, Services, ReplicaSets, etc.)

### Pod with Labels

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-label
  labels:
    team: finance
    version: 1.4.5
    environment: production
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

### Label Commands

```bash
kubectl get pod --show-labels                              # show labels
kubectl label pod <pod-name> <key>=<value>                 # add a label to an existing pod
kubectl label pod <pod-name> <key>=<value> --overwrite     # overwrite an existing label
kubectl get pod -l <key>                                   # pods that have this key
kubectl get pod -l <key>=<value>                           # exact match
kubectl get pod -l '!<key>'                                # pods that don't have this key
kubectl delete pod -l <key>=<value>                        # delete pods by label
```

> **Key Insight:** Labels are not just metadata — they are how Kubernetes objects find each other. Replica Sets, Services, and Daemon Sets all select their Pods by label, never by name.

---

## 📝 Annotations

> **Analogy:** If Labels are short stickers on the outside of a box, Annotations are the detailed shipping manifest folded inside. The contents can be long — a full description, sender notes, a ticket number, a link to documentation — but you can't use them to sort boxes from the outside. They're purely informational.

- **Similar to Labels** — but annotations cannot be used for filtering
- **Purpose** — Used to add large supplemental information to a resource
- **Capacity** — Annotations can hold up to 256KB of data

### Pod with Annotations

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-annotation
  labels:
    team: finance
    environment: production
  annotations:
    description: "This is an application created by the product team"
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

### Annotation Commands

```bash
kubectl annotate pod <pod-name> <key>="<value>"    # add an annotation to an existing pod
```

### Labels vs Annotations

| | Labels | Annotations |
| --- | --- | --- |
| Purpose | Identify and group resources | Attach descriptive information |
| Usable in selectors (`-l`) | Yes | No |
| Size | Short key-value pairs | Up to 256KB |
| Example | `team: finance` | `description: "Created by the product team"` |

---

## 📁 Namespace

> **Analogy:** A Namespace is like different floors in an office building. Floor 1 = Finance team, Floor 2 = Engineering, Floor 3 = Marketing. There can be a "Meeting Room A" on Floor 1 and another "Meeting Room A" on Floor 2 — same name, different locations, no conflict. But don't mistake it for soundproofing: people from Floor 1 can still take the elevator to Floor 2.

- **When to use** — When resources in Kubernetes become too many, or when you need to separate resources for multi-tenant, team, or environment purposes
- **Naming** — Resources with the same name can exist in different namespaces

> **Gotcha:** A Namespace is an organizational separator, not a security wall. Pods in different namespaces can still communicate with each other unless a NetworkPolicy blocks it.

### Namespace Template

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: finance
```

### Namespace Commands

```bash
kubectl get namespaces                               # list all namespaces
kubectl create -f nginx.yaml --namespace finance     # create a resource in a specific namespace
kubectl get pods --namespace finance                 # list pods in a specific namespace
kubectl delete namespace finance                     # delete a namespace
```

> ⚠️ **Warning:** Deleting a namespace deletes **every resource inside it**.

---

## 🔍 Probe

> **Analogy:** Probes are like a health monitoring system for workers in the warehouse. The **Liveness Probe** is a security guard who checks every 10 minutes whether a worker is still responsive — no response means the worker is replaced. The **Readiness Probe** is an HR officer who checks whether a new hire has finished training before assigning them customers. The **Startup Probe** is a special onboarding period for slow starters, during which neither the guard nor HR interferes.

### Probe Types

| Probe | Question It Answers | On Failure |
| --- | --- | --- |
| **Liveness Probe** | Is the container still alive? | Kubelet restarts the container |
| **Readiness Probe** | Is the Pod ready to accept traffic? | The Pod is removed from Service endpoints until it passes |
| **Startup Probe** | Has the application finished starting? | Liveness and readiness checks are held off until it passes — useful for slow-starting Pods |

### Probe Mechanisms

- `httpGet` — HTTP GET request to a path and port
- `tcpSocket` — TCP connection check
- `exec` — Command execution inside the container

### Pod with Probes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-probe
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
      livenessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 5
        periodSeconds: 5
        timeoutSeconds: 1
        successThreshold: 1
        failureThreshold: 3
      readinessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 0
        periodSeconds: 10
        timeoutSeconds: 1
        successThreshold: 1
        failureThreshold: 3
```

### Probe Configuration Fields

| Field | Description | Default |
| --- | --- | --- |
| `initialDelaySeconds` | Delay after the container starts before probing | `0` |
| `periodSeconds` | How often to perform the probe | `10` |
| `timeoutSeconds` | Timeout for each probe | `1` |
| `successThreshold` | Minimum consecutive successes to be considered healthy | `1` |
| `failureThreshold` | Minimum consecutive failures to be considered unhealthy | `3` |

> **Note:** With the liveness settings above, a hung container is restarted after roughly `periodSeconds × failureThreshold` = 5 × 3 = **15 seconds** of failed checks.

---

## 🔁 Replication Controller

> **Analogy:** A Replication Controller is like a factory floor supervisor with one standing mandate: "Make sure there are always exactly 3 workers on this production line." If someone calls in sick, the supervisor immediately recruits a replacement. If too many show up, the supervisor sends the extras home. The headcount stays at 3, 24/7, no exceptions.

- **Purpose** — Ensures Pods are always running; automatically recreates Pods that die or disappear
- **Contents** — Label Selector, Replica Count, and Pod Template

> ⚠️ **Warning:** Replication Controller is deprecated — use [Replica Set](#-replica-set) instead. It's covered here because older tutorials and clusters still use it.

### Replication Controller Template

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

### Replication Controller Commands

```bash
kubectl get rc                                 # list replication controllers
kubectl get replicationcontrollers             # same, full name
kubectl delete rc <rc-name>                    # delete the controller and its pods
kubectl delete rc <rc-name> --cascade=orphan   # delete the controller, keep pods alive
```

---

## 📊 Replica Set

> **Analogy:** Replica Set is the upgraded version of the old supervisor. The old one could only identify workers by a single name tag ("anyone named Ahmad"). The new supervisor is smarter: "I need workers who have a Finance badge AND work the morning OR evening shift." More flexible criteria for identifying and managing the right people.

- **Definition** — The next generation of Replication Controller; the recommended replacement
- **Advantage** — More expressive label selectors using `matchLabels` and `matchExpressions`

### Replica Set Template

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

### Replica Set with matchExpressions

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchExpressions:
      - key: app
        operator: In
        values:
          - nginx
      - key: env
        operator: In
        values:
          - prod
          - qa
          - dev
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
        env: prod
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

### Match Expression Operators

| Operator | Description |
| --- | --- |
| `In` | Label value must be in the list |
| `NotIn` | Label value must not be in the list |
| `Exists` | Label key must exist |
| `DoesNotExist` | Label key must not exist |

> **Gotcha:** The Pod template's labels must satisfy the selector — otherwise Kubernetes rejects the Replica Set, because it could never find the Pods it creates.

### Replica Set Commands

```bash
kubectl get rs    # list replica sets
```

### Replication Controller vs Replica Set

| | Replication Controller | Replica Set |
| --- | --- | --- |
| API version | `v1` | `apps/v1` |
| Selector | Equality only (`app: nginx`) | `matchLabels` and `matchExpressions` |
| Status | Deprecated | Recommended |

---

## 👾 Daemon Set

> **Analogy:** A DaemonSet is like a security guard that must be stationed at every single branch of a company — no exceptions. When the company opens a new warehouse in a new city, a security guard is automatically deployed there. When a warehouse closes, the guard is automatically reassigned. No warehouse is ever left unguarded.

- **Purpose** — Runs exactly one Pod on every Node in the cluster
- **Use cases** — Node monitoring agents, log collection agents, etc.

### Daemon Set Template

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: daemon-nginx
spec:
  selector:
    matchLabels:
      name: daemon-nginx
  template:
    metadata:
      labels:
        name: daemon-nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

> **Note:** A Daemon Set has no `replicas` field — the number of Pods always equals the number of Nodes.

### Daemon Set Commands

```bash
kubectl get daemonsets                       # list daemon sets
kubectl describe daemonsets                  # describe daemon sets
kubectl delete daemonsets <daemonset-name>   # delete a daemon set
```

---

## ⚙️ Job

> **Analogy:** A Job is like a freelance courier hired for one specific task: "Deliver this package to Address X." Once the package is delivered, their job is done and they go home. Unlike a full-time employee (ReplicaSet) who shows up every day regardless, the courier is only called in when there's a delivery — and there's no need to keep them around after it's done.

- **Purpose** — Runs a Pod that only needs to execute once and then stops
- **Behavior** — Unlike a Replica Set, when a Job's Pod finishes, it stays in the `Completed` state
- **Use cases** — Database backup/restore, data import/export, batch processing

### Job Template

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: nodejs-job
spec:
  completions: 4
  parallelism: 2
  template:
    metadata:
      name: nodejs-job
    spec:
      restartPolicy: Never
      containers:
        - name: nodejs-job
          image: khannedy/nodejs-job
```

> **Note:** `completions: 4` means the Job needs 4 successful runs in total; `parallelism: 2` means at most 2 of them run at the same time.

### Job Commands

```bash
kubectl get jobs                  # list jobs
kubectl describe job <job-name>   # describe a job
```

---

## 🕐 Cron Job

> **Analogy:** A CronJob is like an alarm you set on your phone. "Every Monday at 8 AM, remind me to run the database backup." You don't have to remember or press a button manually — the system fires the task automatically on schedule.

- **Purpose** — Schedules Jobs to run at defined times using cron expressions
- **Use cases** — Daily reports, periodic backups, scheduled billing, monthly data pulls

> **Tip:** Test cron expressions at [crontab.guru](https://crontab.guru/). The five fields are `minute hour day-of-month month day-of-week` — so `"* * * * *"` below runs **every minute**.

### Cron Job Template

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nodejs-cronjob
  labels:
    name: nodejs-cronjob
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        metadata:
          labels:
            name: nodejs-cronjob
        spec:
          restartPolicy: Never
          containers:
            - name: nodejs-job
              image: khannedy/nodejs-job
```

### Cron Job Commands

```bash
kubectl get cronjobs                          # list cron jobs
kubectl describe cronjobs <cronjob-name>      # describe a cron job
kubectl delete cronjob <cronjob-name>         # delete a cron job
```

---

## 📍 Node Selector

> **Analogy:** A Node Selector is like a manager's memo to the dispatcher: "This video editing task must be done on a workstation with a dedicated GPU — don't send it to a regular computer." Kubernetes will only place that Pod on a Node labeled `gpu=true`, ensuring the right task always lands on the right machine.

- **Purpose** — Pins a Pod to run on a specific Node based on Node labels
- **Use cases** — Nodes with a GPU, SSD, or other special hardware

### Pod with nodeSelector

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    gpu: "true"
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
```

### Replica Set with nodeSelector

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      nodeSelector:
        hardisk: ssd
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

> **Gotcha:** If no Node carries the matching label, the Pod stays in `Pending` forever. Add the label first with `kubectl label node` (see [Node](#️-node)).

---

## 🌐 Service

> **Analogy:** A Service is like a company's office phone number that never changes. You don't need to know who will pick up — it might be Alice, Bob, or Carol — you just dial the one number and someone responds. If Alice goes on leave and Dan takes over, the number stays the same. A Service does exactly this: one stable address to reach Pods that may come and go freely behind the scenes.

- **Purpose** — Creates a stable gateway to access one or more Pods with a fixed IP and port
- **Benefits** — Clients don't need to know individual Pod locations; Pods can scale up/down without disrupting clients
- **Selection** — Uses label selectors to route traffic to the correct Pods

### Service Types

| Type | Description |
| --- | --- |
| `ClusterIP` | Exposes the Service inside the cluster only (default) |
| `NodePort` | Exposes the Service on each Node's IP at a static port |
| `LoadBalancer` | Exposes the Service externally using a cloud provider's load balancer |
| `ExternalName` | Maps the Service to an external DNS name |

### ClusterIP Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    name: nginx
  ports:
    - port: 8080
      targetPort: 80
```

> **Note:** `port` is what clients connect to on the Service; `targetPort` is the port the container actually listens on.

### NodePort Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    name: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30001
```

### LoadBalancer Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  selector:
    name: nginx
  ports:
    - port: 80
      targetPort: 80
```

### Service Commands

```bash
kubectl get services     # list services
kubectl get endpoints    # list the Pod IPs each service routes to
```

### Accessing a Service from Inside a Pod

Using environment variables:

```bash
env | grep NGINX_SERVICE
```

Using DNS (format: `<service-name>.<namespace>.svc.cluster.local`):

```bash
curl http://nginx-service.default.svc.cluster.local:8080
```

> **Tip:** Prefer DNS. Service environment variables are only injected into Pods created **after** the Service exists, while DNS always resolves.

---

## 🔗 External Service

> **Analogy:** An External Service is like saving a vendor's contact in your company's internal directory. Employees don't need the vendor's real phone number — they just look up the vendor's name, and the system automatically forwards the call to the actual external number. The internal name stays consistent even if the vendor's real number changes.

- **Purpose** — Routes traffic from inside the cluster to external applications outside the Kubernetes cluster

### External Service with Manual Endpoints

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-service
spec:
  ports:
    - port: 80

---

apiVersion: v1
kind: Endpoints
metadata:
  name: external-service
subsets:
  - addresses:
      - ip: 11.11.11.11
      - ip: 22.22.22.22
    ports:
      - port: 80
```

> **Note:** The Service has no `selector`, so Kubernetes doesn't look for Pods — it uses the `Endpoints` object with the **same name** instead.

### External Service with ExternalName

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-service
spec:
  type: ExternalName
  externalName: example.com
  ports:
    - port: 80
```

---

## 🚪 Ingress

> **Analogy:** Imagine a large shopping mall. Without Ingress, every shop has its own entrance directly on the street — visitors need to remember each shop's address. With Ingress, the mall has one main entrance. Inside, a directory board says: "Shop A → turn left, Shop B → take the escalator to Floor 2." Visitors arrive at one door, and the system routes them to the right destination.

- **Problem with NodePort/LoadBalancer** — Every Node or LoadBalancer IP must be exposed publicly; clients need to know all IPs
- **Solution** — Ingress exposes a single entry point; routing is determined by the request hostname
- **Protocol** — Ingress only supports HTTP/HTTPS

### Ingress Template

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  labels:
    name: nginx-ingress
spec:
  ingressClassName: nginx
  rules:
    - host: nginx.dzarurizkyy.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: nginx-service
                port:
                  number: 80
```

### Ingress Commands

```bash
# install the ingress-nginx controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/cloud/deploy.yaml

# list ingresses
kubectl get ingresses
```

> **Gotcha:** An Ingress object does nothing on its own — an **Ingress controller** (such as ingress-nginx above) must be installed to read it. For a local hostname like `nginx.dzarurizkyy.local`, also map it to the cluster IP in `/etc/hosts`.

---

## 🧱 Multi Container Pod

> **Analogy:** A Multi Container Pod is like a shared office room with two employees playing complementary roles. One writes the reports (nodejs-writer), the other presents them to visitors (nginx). They share the same desk (volume) and the same room phone (network). They are a unit — if one relocates, the other relocates too.

- **Concept** — A Pod can contain more than one container; they share the same network and storage
- **Use case** — When multiple containers must always scale together as a unit

### Multi Container Pod with Service

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      name: nginx
  template:
    metadata:
      labels:
        name: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
        - name: nodejs-web
          image: khannedy/nodejs-web
          ports:
            - containerPort: 3000

---

apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    name: nginx
  ports:
    - port: 8080
      targetPort: 80
      name: nginx
    - port: 3000
      targetPort: 3000
      name: nodejs-web
```

> **Note:** Containers in the same Pod share one network namespace, so they reach each other via `localhost` — and each must listen on a different port (`80` and `3000` here). A Service with multiple ports requires a `name` on each one.

---

## 💾 Volume

> **Analogy:** By default, a container is like a whiteboard — when the container is destroyed, everything written on it is erased. A Volume is like a physical notebook placed on the desk. Workers (containers) may come and go, the room (pod) may be reassigned, but the notebook stays. Two workers in the same room can even share the same notebook.

- **Problem** — Files inside a container are ephemeral; they are deleted when the Pod or container is removed
- **Definition** — A Volume is a directory accessible by the containers in a Pod

### Volume Types

| Type | Description |
| --- | --- |
| `emptyDir` | A simple empty directory; exists as long as the Pod lives |
| `hostPath` | Shares a directory from the Node to the Pod |
| `gitRepo` | Clones a git repo into the volume on creation (deprecated) |
| `nfs` | Network File System share |

> **Gotcha:** `emptyDir` survives a container restart but is deleted together with the Pod. For data that must outlive the Pod, use a persistent volume — see [StatefulSet](#-statefulset).

### Pod with emptyDir Volume

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nodejs-writer
spec:
  volumes:
    - name: html
      emptyDir: {}
  containers:
    - name: nodejs-writer
      image: khannedy/nodejs-writer
      volumeMounts:
        - mountPath: /app/html
          name: html
```

### Sharing a Volume Between Containers

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      name: nginx
  template:
    metadata:
      labels:
        name: nginx
    spec:
      volumes:
        - name: html
          emptyDir: {}
      containers:
        - name: nodejs-writer
          image: khannedy/nodejs-writer
          volumeMounts:
            - mountPath: /app/html
              name: html
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
          volumeMounts:
            - mountPath: /usr/share/nginx/html
              name: html
```

---

## 🔧 Environment Variable

> **Analogy:** Environment variables are like a morning briefing before a worker starts their shift: "Today you're stationed at the Surabaya branch, your work folder is on shelf number 3." This information isn't hardcoded into the worker's memory — it's handed to them when they clock in. The same worker can be deployed to a different branch tomorrow with a different briefing.

- **Purpose** — Passes dynamic configuration into containers; avoids hardcoding values in the application

### Pod with Environment Variables

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nodejs-writer
spec:
  volumes:
    - name: html
      emptyDir: {}
  containers:
    - name: nodejs-writer
      image: khannedy/nodejs-writer
      volumeMounts:
        - mountPath: /app/folder-from-env
          name: html
      env:
        - name: HTML_LOCATION
          value: /app/folder-from-env
```

---

## 🗂️ ConfigMap

> **Analogy:** A ConfigMap is like an office bulletin board with information that applies to everyone: "App name: My Cool App. Current version: 1.0.0." Instead of every employee (pod) keeping their own private copy that might get out of sync, one central board is the source.

- **Problem** — Hardcoding environment variables in YAML files means you need separate files per environment (production, development, qa)
- **Solution** — A ConfigMap stores non-sensitive key-value configuration that can be shared across Pods

### ConfigMap Template

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nodejs-env-config
data:
  APPLICATION: My Cool Application
  VERSION: 1.0.0
```

### Using a ConfigMap in a Pod

```yaml
spec:
  containers:
    - name: nodejs-env
      image: khannedy/nodejs-env
      envFrom:
        - configMapRef:
            name: nodejs-env-config
```

> **Gotcha:** Environment variables are read once when the container starts. After updating a ConfigMap, restart the Pods to pick up the new values.

### ConfigMap Commands

```bash
kubectl get configmaps                          # list configmaps
kubectl describe configmap <configmap-name>     # describe a configmap
kubectl delete configmap <configmap-name>       # delete a configmap
```

---

## 🔐 Secret

> **Analogy:** If a ConfigMap is an open bulletin board, a Secret is the locked safe in the HR office. It holds sensitive data — database passwords, API keys, access tokens — that only authorized people should ever see. Kubernetes treats this safe with extra care: the contents are only sent to the specific warehouses (nodes) that need them.

- **Purpose** — Stores sensitive data (passwords, API keys, tokens) more carefully than a ConfigMap
- **Distribution** — Kubernetes only sends a Secret to the Nodes running Pods that use it
- **Storage on Nodes** — Kept in memory (tmpfs), never written to the Node's disk

> ⚠️ **Warning:** By default, Secret values are only **base64-encoded** in etcd, not encrypted — anyone with read access to etcd or the Secret object can decode them. Encryption at rest must be enabled separately by the cluster administrator.

### Secret Template

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: nodejs-env-secret
stringData:
  VERSION: 1.0.0
```

> **Tip:** `stringData` accepts plain text and Kubernetes encodes it for you; the `data` field instead requires values you've already base64-encoded yourself.

### Using ConfigMap and Secret Together

```yaml
spec:
  containers:
    - name: nodejs-env
      image: khannedy/nodejs-env
      envFrom:
        - configMapRef:
            name: nodejs-env-config
        - secretRef:
            name: nodejs-env-secret
```

### Secret Commands

```bash
kubectl get secrets                       # list secrets
kubectl describe secret <secret-name>     # describe a secret (values are hidden)
```

### ConfigMap vs Secret

| | ConfigMap | Secret |
| --- | --- | --- |
| Data | Ordinary settings | Passwords, API keys, tokens |
| Stored as | Plain text | Base64-encoded (encryption at rest optional) |
| Shown by `describe` | Yes | No — only the size |
| Rule of thumb | Fine to see in a public repo | Alarming to see in a public repo |

---

## 📡 Downward API

> **Analogy:** The Downward API is like issuing every employee an ID card that already contains their own details — their name, which room they work in, which floor. The employee doesn't need to call HR to ask "what's my name again?" — all the information about themselves is already on the card.

- **Purpose** — Exposes Pod and Node metadata to containers via environment variables without calling the Kubernetes API directly
- **Note** — The Downward API is not a RESTful API; it's a mechanism to inject runtime information

### Pod with Downward API

```yaml
spec:
  containers:
    - name: nodejs-env
      image: khannedy/nodejs-env
      envFrom:
        - configMapRef:
            name: nodejs-env-config
      env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        - name: POD_NODE
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
        - name: POD_NODE_IP
          valueFrom:
            fieldRef:
              fieldPath: status.hostIP
```

### Available Fields

| `fieldPath` | Value |
| --- | --- |
| `metadata.name` | The Pod's name |
| `metadata.namespace` | The Pod's namespace |
| `status.podIP` | The Pod's IP address |
| `spec.nodeName` | The name of the Node running the Pod |
| `status.hostIP` | The IP address of that Node |

---

## 🛠️ Managing Kubernetes Objects

> **Analogy:** Think of these as the ways you interact with documents at the office. **Create** files a new document and is rejected if one with that name exists. **Apply** creates it if missing and updates it otherwise — the command you'll use most. **Replace** swaps out the whole document for a new version. **Delete** removes it entirely.

### Create vs Apply vs Replace vs Delete

| Operation | Behavior |
| --- | --- |
| **Create** | Creates a new object; fails if it already exists |
| **Apply** | Creates or updates an object; stores the applied configuration in a resource annotation |
| **Replace** | Replaces an existing object with a new definition |
| **Delete** | Removes an object |

### Object Management Commands

```bash
kubectl create -f <filename>.yaml            # create a resource
kubectl apply -f <filename>.yaml             # create or update
kubectl replace -f <filename>.yaml           # replace an existing resource
kubectl get -f <filename>.yaml               # view the resources defined in a file
kubectl get -f <filename>.yaml -o yaml       # ...as YAML
kubectl get -f <filename>.yaml -o json       # ...as JSON
kubectl delete -f <filename>.yaml            # delete the resources defined in a file
kubectl delete all --all                     # delete all resources in the namespace
```

> **Key Insight:** `apply` is **declarative** — running it twice with the same file changes nothing the second time. That makes it safe to re-run, and it's why it's the default choice for real projects.

---

## 📌 StatefulSet

> **Analogy:** There are two ways to manage employees. **The ReplicaSet way (cattle):** all employees are interchangeable — one leaves, hire a new one, just fill the seat. **The StatefulSet way (pets):** each employee has a unique identity. "Employee-0 has locker #1 with their personal data inside." If Employee-0 gets sick, they recover and return to locker #1 — a new hire can't just take a different locker.

- **Pets vs Cattle** — StatefulSet treats Pods like pets (unique identity), while Replica Set treats them like cattle (interchangeable)
- **Purpose** — Manages stateful applications; ensures consistent Pod names, stable network identities, and stable persistent volumes
- **Behavior** — If a Pod dies, StatefulSet recreates it with the same name and identity
- **Use cases** — Databases, message queues, or any system requiring persistent, per-instance data

### StatefulSet Template

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-app
spec:
  serviceName: "my-app"
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: my-app:latest
          ports:
            - containerPort: 8080
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```

> **Note:** `serviceName` must point to a **headless Service** (`clusterIP: None`) — that's what gives each Pod a stable DNS name like `my-app-0.my-app`. `volumeClaimTemplates` gives each Pod its own volume, which is reattached when the Pod is recreated.

### StatefulSet Commands

```bash
kubectl get statefulsets    # list stateful sets
```

### Replica Set vs StatefulSet

| | Replica Set | StatefulSet |
| --- | --- | --- |
| Pod identity | Random names (`nginx-x7k2p`) | Ordered, stable names (`my-app-0`, `my-app-1`) |
| Storage | Shared or none | One persistent volume per Pod |
| Replacement | Any new Pod will do | Recreated with the same name and volume |
| Best for | Stateless applications | Databases and queues |

---

## ⚡ Computational Resources

> **Analogy:** Think of a Node as an office with a limited power supply. Without rules, the busiest employee could plug in all their equipment until the power trips and no one else can work. A **Request** says "this employee needs at least one outlet" — they're only placed in a room with a free outlet. A **Limit** says "this employee gets two outlets at most" — so their neighbors always have power too.

- **Default behavior** — Pods use all available Node resources by default
- **Problem** — A busy Pod can starve other Pods on the same Node

### Request vs Limit

| Term | Description |
| --- | --- |
| `request` | Guaranteed resource amount; Kubernetes only schedules the Pod on a Node with sufficient resources |
| `limit` | Maximum resource the container is allowed to use; it cannot exceed this |

### Pod with Resource Requests and Limits

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
```

> **Note:** Exceeding the two limits behaves differently — a container over its **CPU** limit is throttled (slowed down), while a container over its **memory** limit is killed (`OOMKilled`) and restarted.

### Resource Units

| Resource | Unit | Example |
| --- | --- | --- |
| CPU | `m` (millicores) — `1000m` = 1 CPU core | `250m` = 25% of a core |
| Memory | `Mi` (mebibytes), `Gi` (gibibytes) | `128Mi`, `1Gi` |

> **Gotcha:** For memory, a lowercase `m` means **milli-bytes** — `128m` is 0.128 bytes, not 128 MB. Always use `Mi` or `Gi`.

---

## 📈 Horizontal Pod Autoscaler

> **Analogy:** There are two ways to handle a lunch rush at a restaurant. **Vertical scaling** replaces small tables with bigger ones — it works up to a point, but the room only fits so many tables. **Horizontal scaling** sets up additional tables and calls in extra waitstaff — open 10 tables when it's busy, fold 8 away when it quiets down. HPA does exactly this, automatically.

### Vertical vs Horizontal Scaling

| | Vertical Scaling | Horizontal Scaling |
| --- | --- | --- |
| How | Upgrade CPU/Memory of existing Pods | Add more Pods to distribute load |
| Limit | Capped by Node capacity | Capped by cluster capacity |
| Preferred for scalability | No | Yes |

- **HPA** — A Kubernetes object that automatically scales Pods horizontally based on metrics (CPU, memory)

### HPA Template

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

> **Gotcha:** HPA needs **metrics-server** installed in the cluster, and the target Pods must declare resource **requests** — `averageUtilization: 50` means 50% of the requested CPU, so without a request there's nothing to measure against.

### HPA Commands

```bash
kubectl get hpa                    # list horizontal pod autoscalers
kubectl describe hpa <hpa-name>    # describe an HPA
```

---

## 🎯 Quick Reference

### Resources

| Concept | Purpose | Key Syntax |
| --- | --- | --- |
| **Node** | Worker machine that runs Pods | `kubectl get node` |
| **Pod** | Smallest deployable unit; one or more containers | `kind: Pod` |
| **Labels** | Tag and select resources | `metadata.labels` |
| **Annotations** | Attach non-selectable information | `metadata.annotations` |
| **Namespace** | Group resources; same name allowed across namespaces | `kind: Namespace` |
| **Probe** | Check container health and readiness | `livenessProbe`, `readinessProbe`, `startupProbe` |
| **Replication Controller** | Keep N Pods running (deprecated) | `kind: ReplicationController` |
| **Replica Set** | Keep N Pods running with flexible selectors | `kind: ReplicaSet` |
| **Daemon Set** | One Pod on every Node | `kind: DaemonSet` |
| **Job** | Run a Pod to completion once | `kind: Job` |
| **Cron Job** | Run a Job on a schedule | `schedule: "* * * * *"` |
| **Node Selector** | Pin Pods to labeled Nodes | `nodeSelector: { gpu: "true" }` |
| **Service** | Stable address for a set of Pods | `kind: Service` |
| **External Service** | Internal name for an outside endpoint | `type: ExternalName` |
| **Ingress** | One HTTP(S) entry point with host-based routing | `kind: Ingress` |
| **Volume** | Storage that outlives a container | `volumes` + `volumeMounts` |
| **Environment Variable** | Pass configuration into a container | `env` |
| **ConfigMap** | Shared non-sensitive configuration | `envFrom.configMapRef` |
| **Secret** | Sensitive configuration | `envFrom.secretRef` |
| **Downward API** | Inject Pod/Node metadata | `valueFrom.fieldRef` |
| **StatefulSet** | Pods with stable identity and storage | `kind: StatefulSet` |
| **Resources** | Guarantee and cap CPU/memory | `resources.requests`, `resources.limits` |
| **HPA** | Scale Pods automatically on metrics | `kind: HorizontalPodAutoscaler` |

### kubectl Commands

| Command | Description |
| --- | --- |
| `kubectl get node` | List all nodes |
| `kubectl get pod` | List all pods |
| `kubectl get pod -o wide` | List pods with IP and Node info |
| `kubectl get pod -w` | Watch pod status in real time |
| `kubectl get pod --show-labels` | List pods with labels |
| `kubectl get rs` | List replica sets |
| `kubectl get rc` | List replication controllers |
| `kubectl get daemonsets` | List daemon sets |
| `kubectl get jobs` | List jobs |
| `kubectl get cronjobs` | List cron jobs |
| `kubectl get services` | List services |
| `kubectl get endpoints` | List endpoints |
| `kubectl get namespaces` | List namespaces |
| `kubectl get configmaps` | List configmaps |
| `kubectl get secrets` | List secrets |
| `kubectl get ingresses` | List ingresses |
| `kubectl get statefulsets` | List stateful sets |
| `kubectl get hpa` | List horizontal pod autoscalers |
| `kubectl get all` | List all resources |
| `kubectl describe <type> <name>` | Show detailed info about a resource |
| `kubectl create -f file.yaml` | Create a resource from a file |
| `kubectl apply -f file.yaml` | Create or update a resource from a file |
| `kubectl replace -f file.yaml` | Replace a resource from a file |
| `kubectl delete -f file.yaml` | Delete the resources defined in a file |
| `kubectl delete all --all` | Delete all resources in the namespace |
| `kubectl exec -it <pod> -- /bin/sh` | Open a shell in a pod |
| `kubectl logs <pod>` | View pod logs |
| `kubectl logs -f <pod>` | Follow pod logs in real time |
| `kubectl top pod` / `kubectl top node` | Show CPU and memory usage (requires metrics-server) |
| `kubectl port-forward <pod> <local>:<remote>` | Forward a pod port to localhost |
| `kubectl label pod <pod> <key>=<value>` | Add or update a pod label |
| `kubectl annotate pod <pod> <key>=<value>` | Add or update a pod annotation |

---

## 💡 Best Practices

**✅ Do This**

- **Use Replica Set (or a Deployment) instead of Replication Controller** — Replication Controller is deprecated
- **Always define resource requests and limits** — prevents a single busy Pod from starving others on the same Node, and lets the scheduler make better placement decisions
- **Always add liveness and readiness probes** — liveness restarts unhealthy containers; readiness keeps traffic away from Pods that aren't ready yet
- **Use ConfigMap for configuration, Secret for credentials** — and prefer `stringData` in Secrets to avoid manual base64 encoding
- **Label everything consistently** — standard keys like `app`, `env`, `team`, and `version` make filtering, grouping, and deleting with `-l` much easier
- **Use `kubectl apply` instead of `kubectl create`** — `apply` is idempotent and safe to run repeatedly
- **Separate environments by Namespace** — `production`, `staging`, and `development` each get their own, preventing accidental changes to the wrong one
- **Use Ingress instead of a NodePort or LoadBalancer per service** — one entry point with hostname routing, fewer exposed IPs, and lower cloud load balancer costs
- **Reach Services by DNS name** rather than by IP or environment variable
- **Clean up regularly** — remove unused resources, ConfigMaps, and Secrets with `kubectl delete`
- **Monitor actively** — watch Pods with `kubectl get pod -w`, follow logs with `kubectl logs -f`, and check usage with `kubectl top`

**❌ Avoid This**

- **Hardcoding passwords, API keys, or tokens** in Pod specs or Docker images
- **Assuming a Secret is encrypted** — it's only base64-encoded unless encryption at rest is enabled
- **Creating bare Pods for real workloads** — nothing recreates them when they die
- **Treating Namespaces as a security boundary** — Pods across namespaces can still talk to each other
- **Running Pods without resource limits** — one runaway Pod can take down its whole Node
- **Writing memory as `128m`** — that's milli-bytes; use `Mi` or `Gi`
- **Using a Replica Set for databases** — stateful workloads need a StatefulSet for stable identity and storage
- **Expecting an Ingress to work without an Ingress controller** installed
- **Running `kubectl delete all --all` or deleting a namespace** without double-checking the current namespace first

> Reference: [Kubernetes Documentation](https://kubernetes.io/docs/home/) · [kubectl Quick Reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/)
