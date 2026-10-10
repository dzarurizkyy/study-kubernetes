# Study Kubernetes ☸️

This repository contains a comprehensive reference guide for Kubernetes — covering cluster architecture, core workload resources, networking, configuration, storage, autoscaling, and everyday `kubectl` operations.

## Installation 🔧

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/dzarurizkyy/study-kubernetes.git
   cd study-kubernetes
   ```

2. **Set Up a Local Cluster** (choose one):
   - Install [Docker Desktop](https://www.docker.com/get-started) and enable Kubernetes from the settings panel
   - **or** install [Minikube](https://github.com/kubernetes/minikube) for a dedicated local cluster

3. **Install kubectl**:
   - Follow the [official guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

4. **Verify the Installation**:

   ```bash
   kubectl version --client
   kubectl get node
   ```

5. **Open the Guide**:
   - Start with [Kubernetes Basics](001-kubernetes-basics.md) — every example in it can be applied to the local cluster above

## List of Material 📚

All topics below live in a single chapter — **[Kubernetes Basics](001-kubernetes-basics.md)** — and each entry links straight to its section.

- 🏗️ **[Architecture](001-kubernetes-basics.md#️-kubernetes-architecture)**

  The Kubernetes cluster architecture, including Master components and Worker Node components:

  ```text
  Master  → kube-apiserver, etcd, kube-scheduler, kube-controller-manager
  Node    → kubelet, kube-proxy, container-runtime
  ```

- 🖥️ **[Node](001-kubernetes-basics.md#️-node)**

  A worker machine in the cluster — a VM or physical machine that runs Pods:

  ```bash
  kubectl get node
  kubectl describe node <node-name>
  kubectl label node <node-name> gpu=true
  ```

- 📦 **[Pod](001-kubernetes-basics.md#-pod)**

  The smallest deployable unit in Kubernetes — contains one or more containers that share the same network and storage:

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx
  spec:
    containers:
      - name: nginx
        image: nginx:latest
        ports:
          - containerPort: 80
  ```

- 🏷️ **[Labels](001-kubernetes-basics.md#️-labels) & [Annotations](001-kubernetes-basics.md#-annotations)**

  Labels are used to tag and filter resources. Annotations store supplemental metadata:

  ```bash
  # Add a label
  kubectl label pod <pod-name> env=production
  # Filter pods by label
  kubectl get pod -l env=production
  # Add an annotation
  kubectl annotate pod <pod-name> description="main web server"
  ```

- 📁 **[Namespace](001-kubernetes-basics.md#-namespace)**

  Logical separator for organizing resources across teams or environments:

  ```bash
  kubectl get namespaces
  kubectl create -f nginx.yaml --namespace finance
  kubectl get pods --namespace finance
  ```

- 🔍 **[Probe](001-kubernetes-basics.md#-probe)**

  Health checks that Kubernetes runs against containers to determine liveness and readiness:

  ```yaml
  livenessProbe:
    httpGet:
      path: /
      port: 80
    initialDelaySeconds: 5
    periodSeconds: 5
    failureThreshold: 3
  readinessProbe:
    httpGet:
      path: /
      port: 80
    periodSeconds: 10
  ```

- 🔁 **[Replication Controller](001-kubernetes-basics.md#-replication-controller)**

  The original controller that keeps a fixed number of Pods running — now deprecated in favor of Replica Set:

  ```yaml
  apiVersion: v1
  kind: ReplicationController
  metadata:
    name: nginx-rc
  spec:
    replicas: 3
    selector:
      app: nginx
  ```

- 📊 **[Replica Set](001-kubernetes-basics.md#-replica-set)**

  Ensures a specified number of Pod replicas are always running. Replacement for the deprecated Replication Controller:

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
        containers:
          - name: nginx
            image: nginx
  ```

- 👾 **[Daemon Set](001-kubernetes-basics.md#-daemon-set)**

  Runs exactly one Pod on every Node in the cluster — ideal for monitoring agents and log collectors:

  ```bash
  kubectl get daemonsets
  kubectl delete daemonsets <daemonset-name>
  ```

- ⚙️ **[Job](001-kubernetes-basics.md#️-job) & [Cron Job](001-kubernetes-basics.md#-cron-job)**

  Job runs a task once to completion. CronJob schedules Jobs on a recurring basis using cron expressions:

  ```yaml
  # CronJob — runs every minute
  spec:
    schedule: "* * * * *"
    jobTemplate:
      spec:
        template:
          spec:
            restartPolicy: Never
            containers:
              - name: nodejs-job
                image: khannedy/nodejs-job
  ```

- 📍 **[Node Selector](001-kubernetes-basics.md#-node-selector)**

  Pins a Pod to Nodes that carry a specific label — useful for GPU or SSD machines:

  ```yaml
  spec:
    nodeSelector:
      gpu: "true"
  ```

- 🌐 **[Service](001-kubernetes-basics.md#-service)**

  Provides a stable network endpoint to access Pods. Supports four types: `ClusterIP`, `NodePort`, `LoadBalancer`, and `ExternalName`:

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

- 🔗 **[External Service](001-kubernetes-basics.md#-external-service)**

  Gives applications outside the cluster an internal name, so Pods can reach them like any other Service:

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: external-service
  spec:
    type: ExternalName
    externalName: example.com
  ```

- 🚪 **[Ingress](001-kubernetes-basics.md#-ingress)**

  Single entry point for all external HTTP/HTTPS traffic — routes requests to the correct Service based on hostname:

  ```yaml
  spec:
    ingressClassName: nginx
    rules:
      - host: nginx.example.local
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

- 🧱 **[Multi Container Pod](001-kubernetes-basics.md#-multi-container-pod)**

  Several containers in one Pod that share the same network and always scale together as a unit:

  ```yaml
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
    - name: nodejs-web
      image: khannedy/nodejs-web
      ports:
        - containerPort: 3000
  ```

- 💾 **[Volume](001-kubernetes-basics.md#-volume)**

  Persists data beyond the container lifecycle. Containers in the same Pod can share a volume:

  ```yaml
  volumes:
    - name: html
      emptyDir: {}
  containers:
    - name: writer
      image: khannedy/nodejs-writer
      volumeMounts:
        - mountPath: /app/html
          name: html
    - name: nginx
      image: nginx
      volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: html
  ```

- 🔧 **[Environment Variable](001-kubernetes-basics.md#-environment-variable)**

  Passes dynamic configuration into containers instead of hardcoding it in the application:

  ```yaml
  env:
    - name: HTML_LOCATION
      value: /app/folder-from-env
  ```

- 🗂️ **[ConfigMap](001-kubernetes-basics.md#️-configmap) & [Secret](001-kubernetes-basics.md#-secret)**

  ConfigMap stores non-sensitive configuration. Secret stores sensitive data like passwords and API keys:

  ```yaml
  # ConfigMap
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: app-config
  data:
    APPLICATION: My Cool App
    VERSION: 1.0.0
  ---
  # Secret
  apiVersion: v1
  kind: Secret
  metadata:
    name: app-secret
  stringData:
    DB_PASSWORD: supersecret
  ```

- 📡 **[Downward API](001-kubernetes-basics.md#-downward-api)**

  Exposes Pod and Node metadata to containers as environment variables, without calling the Kubernetes API:

  ```yaml
  env:
    - name: POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
  ```

- 🛠️ **[Managing Kubernetes Objects](001-kubernetes-basics.md#️-managing-kubernetes-objects)**

  The ways to create, update, and remove resources — `apply` creates or updates, `create` fails if the object already exists:

  ```bash
  kubectl create -f nginx.yaml
  kubectl apply -f nginx.yaml
  kubectl delete -f nginx.yaml
  ```

- 📌 **[StatefulSet](001-kubernetes-basics.md#-statefulset)**

  Manages stateful applications where each Pod needs a stable identity and its own persistent storage:

  ```yaml
  apiVersion: apps/v1
  kind: StatefulSet
  metadata:
    name: my-app
  spec:
    serviceName: "my-app"
    replicas: 3
    volumeClaimTemplates:
      - metadata:
          name: data
        spec:
          accessModes: ["ReadWriteOnce"]
          resources:
            requests:
              storage: 1Gi
  ```

- ⚡ **[Computational Resources](001-kubernetes-basics.md#-computational-resources)**

  Defines CPU and memory `requests` (guaranteed) and `limits` (maximum) per container:

  ```yaml
  resources:
    requests:
      memory: "64Mi"
      cpu: "250m"
    limits:
      memory: "128Mi"
      cpu: "500m"
  ```

- 📈 **[Horizontal Pod Autoscaler](001-kubernetes-basics.md#-horizontal-pod-autoscaler)**

  Automatically scales the number of Pod replicas based on CPU or memory utilization:

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

## 📍 References

- [Youtube](https://www.youtube.com/playlist?list=PL-CtdCApEFH8XrWyQAyRd6d_CKwxD8Ime)

## 👨‍💻 Contributors

- [Dzaru Rizky Fathan Fortuna](https://www.linkedin.com/in/dzarurizky)
