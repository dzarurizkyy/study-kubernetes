# Study Kubernetes ☸️

This repository contains a comprehensive reference guide for Kubernetes — covering cluster architecture, Pods and their controllers, Services and Ingress, configuration and storage, resource management, autoscaling, and everyday `kubectl` operations.

## Installation 🔧

1. **Set Up a Local Cluster** (choose one):
   - Install [Docker Desktop](https://www.docker.com/get-started) and enable Kubernetes from the settings panel
   - **or** install [Minikube](https://github.com/kubernetes/minikube) for a dedicated local cluster

2. **Install kubectl**:
   - Follow the [official guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

3. **Verify the Installation**:

   ```bash
   kubectl version --client
   kubectl get node
   ```

   > Reference: [kubernetes.io/docs/tasks/tools](https://kubernetes.io/docs/tasks/tools/)

## List of Material 📚

- ☸️ **[Kubernetes Basics](001-kubernetes-basics.md)**

  Cluster architecture, Pods, labels and annotations, namespaces, probes, Replica Sets, Daemon Sets, Jobs and Cron Jobs, Services, Ingress, volumes, ConfigMaps and Secrets, StatefulSets, computational resources, and the Horizontal Pod Autoscaler:

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
            ports:
              - containerPort: 80
            resources:
              requests:
                memory: "64Mi"
                cpu: "250m"
              limits:
                memory: "128Mi"
                cpu: "500m"
  ```

  Apply it and inspect the result:

  ```bash
  kubectl apply -f nginx.yaml
  kubectl get pod -o wide
  ```

## 📍 References

- [Youtube](https://www.youtube.com/playlist?list=PL-CtdCApEFH8XrWyQAyRd6d_CKwxD8Ime)

## 👨‍💻 Contributors

- [Dzaru Rizky Fathan Fortuna](https://www.linkedin.com/in/dzarurizky)
