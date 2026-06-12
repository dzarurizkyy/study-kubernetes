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

- **Monolith** — An application where all features are built and bundled together in a single unit
- **Microservices** — The opposite of monolith; the application is broken into small, focused services where each handles one task well and they communicate with each other

**Why Docker?**

- Kubernetes supports several container managers
- Docker is currently the most popular and widely used

---

## ☸️ What is Kubernetes?

- **Definition** — Kubernetes is an application for automating deployment, scaling, and management of container-based applications
- **Open Source** — Kubernetes is open source and currently the most popular container orchestration system
- **Widely Adopted** — Many large companies, including unicorns in Indonesia, already use Kubernetes

**History**

- Google ran an internal system called **Borg** (later renamed **Omega**) to help developers and infra engineers manage thousands of servers
- In 2014, Google introduced **Kubernetes** as an open source system based on the experience from Borg, Omega, and other internal systems

---

## 🏗️ Kubernetes Architecture

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

- **Purpose** — Stores sensitive data (passwords, API keys, tokens) in a more secure way than ConfigMap
- **Security** — Kubernetes only distributes a Secret to Nodes that need it; stored in memory (never on physical storage); encrypted at rest in etcd

**Rule of thumb** — Use ConfigMap for non-sensitive config; use Secret for sensitive config

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
