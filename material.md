# ☸️ Kubernetes Basics

A comprehensive reference guide for Kubernetes concepts, resources, and kubectl operations.

---

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [What is Kubernetes?](#-what-is-kubernetes)
- [Kubernetes Architecture](#-kubernetes-architecture)
- [Installation](#-installation)
- [Node](#-node)
- [Pod](#-pod)
- [Labels](#-labels)
- [Annotations](#-annotations)
- [Namespace](#-namespace)
- [Probe](#-probe)
- [Replication Controller](#-replication-controller)
- [Replica Set](#-replica-set)
- [Daemon Set](#-daemon-set)
- [Job](#-job)
- [Cron Job](#-cron-job)
- [Node Selector](#-node-selector)
- [Service](#-service)
- [External Service](#-external-service)
- [Ingress](#-ingress)
- [Multi Container Pod](#-multi-container-pod)
- [Volume](#-volume)
- [Environment Variable](#-environment-variable)
- [ConfigMap](#-configmap)
- [Secret](#-secret)
- [Downward API](#-downward-api)
- [Managing Kubernetes Objects](#-managing-kubernetes-objects)
- [StatefulSet](#-statefulset)
- [Computational Resources](#-computational-resources)
- [Horizontal Pod Autoscaler](#-horizontal-pod-autoscaler)
- [Quick Reference Table](#-quick-reference-table)

---

## 🧩 Prerequisites

**From Monolith to Microservices**

> **Analogy:** Think of a traditional restaurant with one big kitchen handling everything — cooking rice, frying chicken, making drinks — all in the same place. If the stove breaks, the entire restaurant shuts down. A modern restaurant separates concerns: one booth for drinks, one for main courses, one for desserts. If the drinks booth has an issue, food service keeps running. That's the difference between a monolith and microservices.

- **Monolith** — An application where all features are built and bundled together in a single unit
- **Microservices** — The opposite of monolith; the application is broken into small, focused services where each handles one task well and they communicate with each other

**Why Docker?**

> **Analogy:** Docker is like a lunchbox. You pack your meal together with all the ingredients and utensils inside the box. No matter whose kitchen you open it in, the food comes out exactly the same — you never have to worry about "it works on my machine but not on yours."

- Kubernetes supports several container managers
- Docker is currently the most popular and widely used

---

## ☸️ What is Kubernetes?

> **Analogy:** Kubernetes is like the operations manager of a large franchise chain. You have hundreds of branches (servers), each with their own staff (applications). Without a manager, you'd have to personally visit every branch to make sure they're open, properly staffed, and not overwhelmed. Kubernetes does all of this automatically — opening new branches when demand spikes, closing idle ones, and relocating staff when a building suddenly goes offline.

- **Definition** — Kubernetes is an application for automating deployment, scaling, and management of container-based applications
- **Open Source** — Kubernetes is open source and currently the most popular container orchestration system
- **Widely Adopted** — Many large companies, including unicorns in Indonesia, already use Kubernetes

**History**

- Google ran an internal system called **Borg** (later renamed **Omega**) to help developers and infra engineers manage thousands of servers
- In 2014, Google introduced **Kubernetes** as an open source system based on the experience from Borg, Omega, and other internal systems

---

## 🏗️ Kubernetes Architecture

> **Analogy: A Logistics Company**
>
> Think of Kubernetes as a large package delivery company.
> - **You (the developer)** → a customer who wants to ship many packages to different cities
> - **Kubernetes** → the logistics company that handles everything
>
> ---
>
> **Master = Head Office**
>
> **kube-apiserver — The Front Desk Receptionist**
> You can't walk directly into the warehouse or talk to drivers. Every instruction must go through the receptionist. All other internal components also only communicate through the apiserver — never directly with each other.
>
> **etcd — The Official Record Book**
> This is the single source of truth for all status: "Warehouse in City A has 3 active drivers", "Package #A023 is on its way to City B." If the head office crashes and restarts, everything is restored from this book.
>
> **kube-scheduler — The Dispatcher**
> One job only: "Which warehouse and driver is the best fit for this package?" It checks which nodes have spare capacity and the right requirements, then assigns the pod there. Nothing more, nothing less.
>
> **kube-controller-manager — The Operations Manager**
> Constantly ensures that reality matches what you asked for. You requested 3 pods running; only 2 are alive → the controller says "We're one short, spin up a new one!" This non-stop loop is the heart of Kubernetes' self-healing.
>
> **cloud-controller-manager — The Vendor Liaison**
> If the office rents space from a cloud provider (AWS, GCP, Azure), this person handles all requests to the vendor: "Please provision a new load balancer", "Please add more cloud storage." If you're running on-premise, this component is not used.
>
> ---
>
> **Worker Nodes = Warehouses and Drivers in the Field**
>
> **kubelet — The Warehouse Supervisor**
> Receives instructions from head office: "Run container image nginx:latest here." Executes it and reports back: "Done / Failed."
>
> **kube-proxy — The Traffic Director**
> When a request arrives for "Service A" → kube-proxy routes it to the right pod. Also handles load balancing across pods on the same node.
>
> **container-runtime — The Driver and Truck**
> The component that actually runs the container. Could be Docker, containerd, etc. Kubelet says "run this" → container-runtime executes it at the OS level.

**Kubernetes Master**

- **kube-apiserver** — Acts as the API used to interact with the Kubernetes Cluster
- **etcd** — The database used to store Kubernetes Cluster data
- **kube-scheduler** — Monitors running applications and assigns them to Nodes
- **kube-controller-manager** — Controls the overall state of the Kubernetes Cluster
- **cloud-controller-manager** — Manages interaction with the cloud provider

**Kubernetes Nodes**

- **kubelet** — Runs on every Node and ensures applications are running
- **kube-proxy** — Runs on every Node, acts as a network proxy and load balancer
- **container-manager** — Runs on every Node and manages containers (Docker, containerd, cri-o, rktlet, etc.)

---

## 🛠️ Installation

**Installing Kubernetes Locally**

- **Docker Desktop** — Enable Kubernetes directly from Docker Desktop settings
- **Minikube** — Requires VirtualBox or Hyper-V ([github.com/kubernetes/minikube](https://github.com/kubernetes/minikube))

**Installing kubectl**

```bash
# Follow the official guide
# https://kubernetes.io/docs/tasks/tools/install-kubectl/
```

---

## 🖥️ Node

> **Analogy:** A Node is like a warehouse building in a city. Inside one warehouse (node), there can be many workrooms (pods). Kubernetes can manage many warehouses across many cities — all coordinated from the head office.

- **Definition** — A Node is a worker machine in Kubernetes (also called a Minion); can be a VM or physical machine
- Every Node always has `kubelet`, `kube-proxy`, and a container manager

**List all nodes**

```bash
kubectl get node
```

**Describe a specific node**

```bash
kubectl describe node <node-name>
```

Example:

```bash
kubectl describe node orbstack
```

**Add a label to a node**

```bash
kubectl label node <node-name> <key>=<value>
```

Example:

```bash
kubectl label node orbstack gpu=true
```

---

## 📦 Pod

> **Analogy:** A Pod is like a single workroom inside a warehouse. The room can have one or more workers (containers) sharing the same desk and phone line. If the room is demolished, all workers inside go with it — but Kubernetes can immediately build a new identical room to replace it.

- **Definition** — A Pod is the smallest deployable unit in Kubernetes; it contains one or more containers
- **Purpose** — A Pod is essentially your application running inside the Kubernetes Cluster

**Pod template**

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

**List all pods**

```bash
kubectl get pod
kubectl get pod -o wide     # includes IP and Node info
kubectl get pod -w          # watch for changes
```

**Describe a pod**

```bash
kubectl describe pod <pod-name>
```

**Create a pod from a file**

```bash
kubectl create -f nginx.yaml
```

**Delete a pod**

```bash
kubectl delete pod <pod-name>
kubectl delete pod --all
```

**Forward a port from pod to host**

```bash
kubectl port-forward <pod-name> <host-port>:<container-port>
```

Example:

```bash
kubectl port-forward nginx 8888:80
```

**Access a shell inside a pod**

```bash
kubectl exec -it <pod-name> -- /bin/sh
```

---

## 🏷️ Labels

> **Analogy:** Labels are like colored sticker tags on shipping boxes. A red sticker means "Finance team", a blue sticker means "Production environment", a yellow one means "Version 1.4.5." With these stickers, you can instantly find all Finance-team boxes that are still on version 1.x — without opening each box individually.

- **Purpose** — Labels are used to tag, organize, and add information to Pods and other resources
- Labels can be applied to any resource in Kubernetes (Pods, Services, ReplicaSets, etc.)

**Pod template with labels**

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

**Show labels**

```bash
kubectl get pod --show-labels
```

**Add a label to an existing pod**

```bash
kubectl label pod <pod-name> <key>=<value>
```

**Overwrite an existing label**

```bash
kubectl label pod <pod-name> <key>=<value> --overwrite
```

**Filter pods by label**

```bash
kubectl get pod -l <key>                  # pods that have this key
kubectl get pod -l <key>=<value>          # exact match
kubectl get pod -l '!<key>'              # pods that don't have this key
```

**Delete pods by label**

```bash
kubectl delete pod -l <key>=<value>
```

---

## 📝 Annotations

> **Analogy:** If Labels are short stickers on the outside of a box, Annotations are the detailed shipping manifest folded inside. The contents can be long — a full description, sender notes, a ticket number, a link to documentation — but you can't use them to sort boxes from the outside. They're purely informational.

- **Similar to Labels** — but annotations cannot be used for filtering
- **Purpose** — Used to add large supplemental information to a resource
- **Capacity** — Annotations can hold up to 256KB of data

**Pod template with annotations**

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

**Add an annotation to an existing pod**

```bash
kubectl annotate pod <pod-name> <key>="<value>"
```

---

## 📁 Namespace

> **Analogy:** A Namespace is like different floors in an office building. Floor 1 = Finance team, Floor 2 = Engineering, Floor 3 = Marketing. There can be a "Meeting Room A" on Floor 1 and another "Meeting Room A" on Floor 2 — same name, different locations, no conflict. But don't mistake it for soundproofing: people from Floor 1 can still take the elevator to Floor 2. Namespaces are an organizational separator, not a security wall.

- **When to use** — When resources in Kubernetes become too many, or when you need to separate resources for multi-tenant, team, or environment purposes
- **Note** — Resources with the same name can exist in different namespaces
- **Isolation caveat** — Pods in different namespaces can still communicate with each other; namespace is not an isolation mechanism

**List all namespaces**

```bash
kubectl get namespaces
```

**Namespace template**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: finance
```

**Create resource in a specific namespace**

```bash
kubectl create -f nginx.yaml --namespace finance
```

**List pods in a specific namespace**

```bash
kubectl get pods --namespace finance
```

**Delete a namespace**

```bash
kubectl delete namespace finance
```

---

## 🔍 Probe

> **Analogy:** Probes are like a health monitoring system for workers in the warehouse.
> - **Liveness Probe** → like a security guard who checks every 10 minutes whether a worker is still conscious and responsive. No response = the worker is considered incapacitated and gets replaced with a fresh one.
> - **Readiness Probe** → like an HR officer who checks whether a new hire has finished training and is ready to take on customer requests. Until they're ready, no tasks are assigned to them.
> - **Startup Probe** → like a special onboarding period for slow-starting employees. During onboarding, neither the security guard nor HR interferes — they wait patiently until the employee signals they're ready.

- **Liveness Probe** — Kubelet uses this to determine when to restart a Pod (e.g., if it becomes unresponsive)
- **Readiness Probe** — Kubelet uses this to check whether a Pod is ready to accept traffic
- **Startup Probe** — Kubelet uses this to check if the Pod has started; disables liveness/readiness checks until startup is confirmed; useful for slow-starting Pods

**Probe mechanisms**

- `httpGet` — HTTP GET request to a path and port
- `tcpSocket` — TCP connection check
- `exec` — Command execution inside the container

**Pod template with probes**

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

**Probe configuration fields**

| Field | Description | Default |
|-------|-------------|---------|
| `initialDelaySeconds` | Delay after container starts before probing | `0` |
| `periodSeconds` | How often to perform the probe | `10` |
| `timeoutSeconds` | Timeout for each probe | `1` |
| `successThreshold` | Minimum consecutive successes to be considered healthy | `1` |
| `failureThreshold` | Minimum consecutive failures to be considered unhealthy | `3` |

---

## 🔁 Replication Controller

> **Analogy:** A Replication Controller is like a factory floor supervisor with one standing mandate: "Make sure there are always exactly 3 workers on this production line." If someone calls in sick, the supervisor immediately recruits a replacement. If too many show up, the supervisor sends the extras home. The headcount stays at 3, 24/7, no exceptions.
>
> *(Note: This supervisor model is retired — replaced by the smarter Replica Set.)*

- **Purpose** — Ensures Pods are always running; automatically restarts Pods that die or disappear
- **Contents** — Label Selector, Replica Count, and Pod Template
- **Note** — Replication Controller is deprecated; use Replica Set instead

**Replication Controller template**

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

**List replication controllers**

```bash
kubectl get rc
kubectl get replicationcontrollers
```

**Delete replication controller (and its pods)**

```bash
kubectl delete rc <rc-name>
```

**Delete replication controller (keep pods alive)**

```bash
kubectl delete rc <rc-name> --cascade=orphan
```

---

## 📊 Replica Set

> **Analogy:** Replica Set is the upgraded version of the old supervisor. The old one could only identify workers by a single name tag ("anyone named Ahmad"). The new supervisor is smarter: "I need workers who have a Finance badge AND work the morning OR evening shift." More flexible criteria for identifying and managing the right people.

- **Definition** — The next generation of Replication Controller; recommended replacement
- **Advantage** — More expressive label selectors using `matchLabels` and `matchExpressions`

**Replica Set template**

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

**Replica Set with matchExpressions**

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

**Match expression operators**

| Operator | Description |
|----------|-------------|
| `In` | Label value must be in the list |
| `NotIn` | Label value must not be in the list |
| `Exists` | Label key must exist |
| `DoesNotExist` | Label key must not exist |

**List replica sets**

```bash
kubectl get rs
```

---

## 👾 Daemon Set

> **Analogy:** A DaemonSet is like a security guard that must be stationed at every single branch of a company — no exceptions. When the company opens a new warehouse in a new city, a security guard is automatically deployed there. When a warehouse closes, the guard is automatically reassigned. No warehouse is ever left unguarded.

- **Purpose** — Runs exactly one Pod on every Node in the cluster
- **Use cases** — Node monitoring agents, log collection agents, etc.

**DaemonSet template**

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

**List daemon sets**

```bash
kubectl get daemonsets
kubectl describe daemonsets
```

**Delete a daemon set**

```bash
kubectl delete daemonsets <daemonset-name>
```

---

## ⚙️ Job

> **Analogy:** A Job is like a freelance courier hired for one specific task: "Deliver this package to Address X." Once the package is delivered, their job is done and they go home. Unlike a full-time employee (ReplicaSet) who shows up every day regardless, the courier is only called in when there's a delivery — and there's no need to keep them around after it's done.

- **Purpose** — Runs a Pod that only needs to execute once and then stops
- **Behavior** — Unlike ReplicaSet, when a Job's Pod finishes, it stays in `Completed` state
- **Use cases** — Database backup/restore, data import/export, batch processing

**Job template**

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

**List and describe jobs**

```bash
kubectl get jobs
kubectl describe job <job-name>
```

---

## 🕐 Cron Job

> **Analogy:** A CronJob is like an alarm you set on your phone. "Every Monday at 8 AM, remind me to run the database backup." You don't have to remember or press a button manually — the system fires the task automatically on schedule. Just like a monthly subscription payment that gets auto-charged on the same date every month.

- **Purpose** — Schedules Jobs to run at defined times using cron expressions
- **Use cases** — Daily reports, periodic backups, scheduled billing, monthly data pulls

**Test cron expressions:** [crontab.guru](https://crontab.guru/)

**Cron Job template**

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

**List and describe cron jobs**

```bash
kubectl get cronjobs
kubectl describe cronjobs <cronjob-name>
```

**Delete a cron job**

```bash
kubectl delete cronjob <cronjob-name>
```

---

## 📍 Node Selector

> **Analogy:** A Node Selector is like a manager's memo to the dispatcher: "This video editing task must be done on a workstation with a dedicated GPU — don't send it to a regular computer." Kubernetes will only place that pod on a node labeled `gpu=true`, ensuring the right task always lands on the right machine.

- **Purpose** — Pins a Pod to run on a specific Node based on Node labels
- **Use cases** — Nodes with GPU, SSD, or other special hardware

**Pod with nodeSelector**

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

**ReplicaSet with nodeSelector**

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

---

## 🌐 Service

> **Analogy:** A Service is like a company's office phone number that never changes. You don't need to know who will pick up — it might be Alice, Bob, or Carol — you just dial the one number and someone responds. If Alice goes on leave and Dan takes over, the number stays the same. A Service does exactly this: one stable address to reach Pods that may come and go freely behind the scenes.

- **Purpose** — Creates a stable gateway to access one or more Pods with a fixed IP and port
- **Benefits** — Clients don't need to know individual Pod locations; Pods can scale up/down without disrupting clients
- **Selection** — Uses label selectors to route traffic to the correct Pods

**Service types**

| Type | Description |
|------|-------------|
| `ClusterIP` | Exposes the Service inside the cluster only (default) |
| `NodePort` | Exposes the Service on each Node's IP at a static port |
| `LoadBalancer` | Exposes the Service externally using a cloud provider's load balancer |
| `ExternalName` | Maps the Service to an external DNS name |

**Service template (ClusterIP)**

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

**Service template (NodePort)**

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

**Service template (LoadBalancer)**

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

**List services and endpoints**

```bash
kubectl get services
kubectl get endpoints
```

**Accessing a Service from inside a Pod**

Using environment variable:
```bash
env | grep NGINX_SERVICE
```

Using DNS (format: `<service-name>.<namespace>.svc.cluster.local`):
```bash
curl http://nginx-service.default.svc.cluster.local:8080
```

---

## 🔗 External Service

> **Analogy:** An External Service is like saving a vendor's contact in your company's internal directory. Employees don't need the vendor's real phone number — they just look up the vendor's name in the internal directory, and the system automatically forwards the call to the actual external number. The internal name stays consistent even if the vendor's real number changes.

- **Purpose** — Used to route traffic to external applications outside the Kubernetes cluster

**External Service with manual endpoints**

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

**External Service with ExternalName**

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

> **Analogy:** Imagine a large shopping mall. Without Ingress, every shop has its own entrance directly on the street — visitors need to remember each shop's address. With Ingress, the mall has one main entrance (the lobby). Inside, a directory board says: "Shop A → turn left, Shop B → take the escalator to Floor 2." Visitors arrive at one door, and the system routes them to the right destination. One front door for everything.

- **Problem with NodePort/LoadBalancer** — Every Node or LoadBalancer IP must be exposed publicly; clients need to know all IPs
- **Solution** — Ingress exposes a single entry point; routing is determined by the request hostname
- **Protocol** — Ingress only supports HTTP/HTTPS

**Ingress template**

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

**Install ingress-nginx controller**

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/cloud/deploy.yaml
```

**List ingresses**

```bash
kubectl get ingresses
```

---

## 🧱 Multi Container Pod

> **Analogy:** A Multi Container Pod is like a shared office room with two employees playing complementary roles. One writes the reports (nodejs-writer), the other presents them to visitors (nginx). They share the same desk (volume) and the same room phone (network). They are a unit — if one relocates, the other relocates too. You can't split them up.

- **Concept** — In Kubernetes, a Pod can contain more than one container; they share the same network and storage
- **Use case** — When multiple containers must always scale together as a unit

**Multi-container Pod with Service**

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

---

## 💾 Volume

> **Analogy:** By default, a container is like a whiteboard — when the container is destroyed, everything written on it is erased. A Volume is like a physical notebook placed on the desk. Workers (containers) may come and go, the room (pod) may be reassigned, but the notebook stays. Two workers in the same room can even share the same notebook and read each other's entries.

- **Problem** — Files inside a container are ephemeral; they are deleted when the Pod or container is removed
- **Definition** — A Volume is a directory accessible by containers in a Pod

**Volume types**

| Type | Description |
|------|-------------|
| `emptyDir` | A simple empty directory; exists as long as the Pod lives |
| `hostPath` | Shares a directory from the Node to the Pod |
| `gitRepo` | Clones a git repo into the volume on creation |
| `nfs` | Network File System share |

**Pod with emptyDir volume**

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

**Sharing a volume between containers**

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

> **Analogy:** Environment variables are like a morning briefing before a worker starts their shift: "Today you're stationed at the Surabaya branch, your work folder is on shelf number 3." This information isn't hardcoded into the worker's memory — it's handed to them when they clock in. The same worker can be deployed to a different branch tomorrow with a different briefing, no retraining required.

- **Purpose** — Passes dynamic configuration into containers; avoids hardcoding values in the application

**Pod with environment variables**

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

> **Analogy:** A ConfigMap is like an office bulletin board with information that applies to everyone: "App name: My Cool App. Current version: 1.0.0." Instead of every employee (pod) keeping their own private copy that might get out of sync, one central board is the source. If something changes, update the board once — everyone reads it automatically.

- **Problem** — Hardcoding environment variables in YAML files means you need separate files per environment (production, development, qa)
- **Solution** — ConfigMap stores non-sensitive key-value configuration that can be shared across Pods

**ConfigMap template**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nodejs-env-config
data:
  APPLICATION: My Cool Application
  VERSION: 1.0.0
```

**Using ConfigMap in a Pod**

```yaml
spec:
  containers:
    - name: nodejs-env
      image: khannedy/nodejs-env
      envFrom:
        - configMapRef:
            name: nodejs-env-config
```

**List and describe ConfigMaps**

```bash
kubectl get configmaps
kubectl describe configmap <configmap-name>
```

**Delete a ConfigMap**

```bash
kubectl delete configmap <configmap-name>
```

---

## 🔐 Secret

> **Analogy:** If a ConfigMap is an open bulletin board, a Secret is the locked safe in the HR office. It holds sensitive data — database passwords, API keys, access tokens — that only authorized people should ever see. Kubernetes treats this safe with extra care: the contents are only sent to the specific warehouses (nodes) that need them, stored only in memory (never written to disk), and encrypted at rest.
>
> **Rule of thumb:** Use ConfigMap for ordinary settings. Use Secret for anything you'd be alarmed to see in a public GitHub repository.

- **Purpose** — Stores sensitive data (passwords, API keys, tokens) in a more secure way than ConfigMap
- **Security** — Kubernetes only distributes a Secret to Nodes that need it; stored in memory (never on physical storage); encrypted at rest in etcd

**Secret template**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: nodejs-env-secret
stringData:
  VERSION: 1.0.0
```

**Using both ConfigMap and Secret in a Pod**

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

**List and describe Secrets**

```bash
kubectl get secrets
kubectl describe secret <secret-name>
```

---

## 📡 Downward API

> **Analogy:** The Downward API is like issuing every employee an ID card that already contains their own details — their name, which room they work in, which floor, what their desk's extension number is. The employee doesn't need to call HR to ask "what's my name again?" — all the information about themselves is already on the card they received when they started.

- **Purpose** — Exposes Pod and Node metadata to containers via environment variables without calling the Kubernetes API directly
- **Note** — Downward API is not a RESTful API; it's a mechanism to inject runtime information

**Pod with Downward API**

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

---

## 🛠️ Managing Kubernetes Objects

> **Analogy:** Think of these as the ways you interact with documents at the office:
> - **Create** → File a new document. If a document with that name already exists, the system rejects it.
> - **Apply** → "Apply this document." If it doesn't exist yet, create it. If it does, update it. This is the command you'll use most often.
> - **Replace** → Swap out the entire document with a new version from scratch.
> - **Delete** → Remove the document entirely.

- **Create** — Creates a new object; fails if it already exists
- **Apply** — Creates or updates an object; stores the configuration in the resource annotation
- **Replace** — Replaces an existing object with a new definition
- **Delete** — Removes an object

**Create a resource**

```bash
kubectl create -f <filename>.yaml
```

**Apply (create or update)**

```bash
kubectl apply -f <filename>.yaml
```

**View a resource defined in a file**

```bash
kubectl get -f <filename>.yaml
kubectl get -f <filename>.yaml -o yaml
kubectl get -f <filename>.yaml -o json
```

**Delete a resource**

```bash
kubectl delete -f <filename>.yaml
```

**Delete all resources**

```bash
kubectl delete all --all
```

---

## 📌 StatefulSet

> **Analogy:** There are two ways to manage employees at a company:
> - **The ReplicaSet way (cattle):** All employees are interchangeable. One leaves, hire a new one — it doesn't matter who, just fill the seat.
> - **The StatefulSet way (pets):** Each employee has a unique identity that cannot simply be replaced. "Employee-0 has locker #1 with their personal data inside." If Employee-0 gets sick, they recover and return to locker #1 — a new hire can't just take a different locker and call it done.
>
> StatefulSet is the right choice for applications that need to "remember who they are" — like databases, message queues, or any system requiring persistent, per-instance data.

- **Pets vs Cattle** — StatefulSet treats Pods like pets (unique identity), while ReplicaSet treats them like cattle (interchangeable)
- **Purpose** — Manages stateful applications; ensures consistent Pod names, stable network identities, and stable persistent volumes
- **Behavior** — If a Pod dies, StatefulSet recreates it with the same name and identity

**StatefulSet template**

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

**List StatefulSets**

```bash
kubectl get statefulsets
```

---

## ⚡ Computational Resources

> **Analogy:** Think of a Node as an office with a limited power supply. Without rules, the busiest employee (the most active pod) could plug in all their equipment — PC, monitors, heaters, chargers — until the power trips and no one else can work. Resource management prevents that:
> - **Request** → "This employee needs at least one power outlet to function." Kubernetes only places them in a room that has a free outlet.
> - **Limit** → "This employee is allowed a maximum of two outlets." No matter how much work they have, they cannot exceed that — so their neighbors always have power too.

- **Default behavior** — Pods use all available Node resources by default
- **Problem** — A busy Pod can starve other Pods on the same Node

**Request vs Limit**

| Term | Description |
|------|-------------|
| `request` | Guaranteed resource amount; Kubernetes only schedules the Pod on a Node with sufficient resources |
| `limit` | Maximum resource the container is allowed to use; it cannot exceed this |

**Pod with resource requests and limits**

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

**CPU units** — `1000m` (millicores) = 1 CPU core; `250m` = 25% of a core

**Memory units** — `Mi` (mebibytes), `Gi` (gibibytes), `m` (milli-bytes for very small amounts)

---

## 📈 Horizontal Pod Autoscaler

> **Analogy:** There are two ways to handle a lunch rush at a restaurant:
> - **Vertical Scaling (upgrade the table):** Replace small tables with bigger ones. Works up to a point — the room only fits so many tables.
> - **Horizontal Scaling (open more tables):** Set up additional tables and call in extra waitstaff. When it's busy, open 10 tables. When it quiets down, fold 8 of them away. HPA does exactly this — automatically adding or removing Pods based on how busy the system is (CPU usage, memory, etc.).

- **Vertical Scaling** — Upgrade CPU/Memory of existing Pods; limited by Node capacity
- **Horizontal Scaling** — Add more Pods to distribute load; preferred for scalability
- **HPA** — Kubernetes object that automatically scales Pods horizontally based on metrics (CPU, memory)

**HPA template**

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

**List HPAs**

```bash
kubectl get hpa
kubectl describe hpa <hpa-name>
```

---

## 🎯 Quick Reference Table

| Command | Description |
|---------|-------------|
| `kubectl get node` | List all nodes |
| `kubectl get pod` | List all pods |
| `kubectl get pod -o wide` | List pods with IP and Node info |
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
| `kubectl create -f file.yaml` | Create resource from file |
| `kubectl apply -f file.yaml` | Create or update resource from file |
| `kubectl delete -f file.yaml` | Delete resource defined in file |
| `kubectl delete all --all` | Delete all resources in namespace |
| `kubectl exec -it <pod> -- /bin/sh` | Open shell in a pod |
| `kubectl logs <pod>` | View pod logs |
| `kubectl port-forward <pod> <local>:<remote>` | Forward a pod port to localhost |
| `kubectl label pod <pod> <key>=<value>` | Add or update a pod label |
| `kubectl annotate pod <pod> <key>=<value>` | Add or update a pod annotation |

---

## 💡 Tips & Best Practices

- **Always use Replica Set over Replication Controller**
  - Replication Controller is deprecated; use Replica Set or Deployment instead

- **Always define resource requests and limits**
  - Prevents a single busy Pod from starving other Pods on the same Node
  - Enables the scheduler to make better placement decisions

  ```yaml
  resources:
    requests:
      memory: "64Mi"
      cpu: "250m"
    limits:
      memory: "128Mi"
      cpu: "500m"
  ```

- **Always add liveness and readiness probes**
  - Liveness probe restarts unhealthy containers automatically
  - Readiness probe prevents traffic from being sent to Pods that aren't ready yet

- **Use ConfigMap for configuration, Secret for credentials**
  - Never hardcode passwords, API keys, or tokens in Pod specs or Docker images
  - Use `stringData` in Secrets to avoid manual base64 encoding

- **Label everything consistently**
  - Use standard label keys like `app`, `env`, `team`, `version` across all resources
  - Makes filtering, grouping, and deletion with `-l` much easier

- **Use `kubectl apply` instead of `kubectl create`**
  - `apply` is idempotent — safe to run repeatedly; stores config for future diffs
  - `create` fails if the resource already exists

- **Prefer Namespace separation over mixing environments**
  - Use separate namespaces for `production`, `staging`, and `development`
  - Prevents accidental changes to the wrong environment

- **Use Ingress instead of NodePort or LoadBalancer per service**
  - A single Ingress controller handles all external traffic via hostname routing
  - Reduces the number of exposed IPs and cloud load balancer costs

- **Regular cleanup**

  Remove all resources in a namespace:

  ```bash
  kubectl delete all --all
  ```

  Remove unused config and secrets:

  ```bash
  kubectl delete configmap <name>
  kubectl delete secret <name>
  ```

- **Monitoring**

  Watch pod status in real-time:

  ```bash
  kubectl get pod -w
  ```

  View pod logs:

  ```bash
  kubectl logs <pod-name>
  kubectl logs -f <pod-name>        # follow in real-time
  ```

  Check resource usage per pod:

  ```bash
  kubectl top pod
  kubectl top node
  ```
