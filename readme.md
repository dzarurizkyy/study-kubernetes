# Study Kubernetes ☸️
This repository contains a comprehensive reference guide for Kubernetes concepts, resources, and kubectl operations. It covers everything from cluster architecture and core workload resources to networking, configuration, storage, and autoscaling.

## Installation 🔧
1. Install [Docker Desktop](https://www.docker.com/get-started) and enable Kubernetes from the settings panel, **or** install [Minikube](https://github.com/kubernetes/minikube) for a dedicated local cluster
2. Install `kubectl` from the [official guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
3. Verify installation:
   ```bash
   kubectl version --client
   kubectl get node
   ```

## List of Material 📚

* 📘 **Architecture**
  
  Covers the Kubernetes cluster architecture including Master components and Worker Node components:
  ```
  Master  → kube-apiserver, etcd, kube-scheduler, kube-controller-manager
  Node    → kubelet, kube-proxy, container-runtime
  ```

* 📗 **Pod**
  
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

* 📗 **Labels & Annotations**
  
  Labels are used to tag and filter resources. Annotations store supplemental metadata:
  ```bash
  # Add a label
  kubectl label pod <pod-name> env=production
  # Filter pods by label
  kubectl get pod -l env=production
  # Add an annotation
  kubectl annotate pod <pod-name> description="main web server"
  ```

* 📗 **Namespace**
  
  Logical separator for organizing resources across teams or environments:
  ```bash
  kubectl get namespaces
  kubectl create -f nginx.yaml --namespace finance
  kubectl get pods --namespace finance
  ```

* 📘 **Probe**
  
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

* 📗 **Replica Set**
  
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

* 📗 **Daemon Set**
  
  Runs exactly one Pod on every Node in the cluster — ideal for monitoring agents and log collectors:
  ```bash
  kubectl get daemonsets
  kubectl delete daemonsets <daemonset-name>
  ```

* 📗 **Job & Cron Job**
  
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

* 📘 **Service**
  
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

* 📗 **Ingress**
  
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

* 📙 **ConfigMap & Secret**
  
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

  # Secret
  apiVersion: v1
  kind: Secret
  metadata:
    name: app-secret
  stringData:
    DB_PASSWORD: supersecret
  ```

* 📙 **Volume**
  
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

* 📙 **StatefulSet**
  
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

* 📘 **Computational Resources**
  
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

* 📘 **Horizontal Pod Autoscaler**
  
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
* [Youtube](https://www.youtube.com/playlist?list=PL-CtdCApEFH8XrWyQAyRd6d_CKwxD8Ime)

## 👨‍💻 Contributors
* [Dzaru Rizky Fathan Fortuna](https://www.linkedin.com/in/dzarurizky)
