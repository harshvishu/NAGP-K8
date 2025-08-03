# Todo API with Postgres on Kubernetes

This project deploys a **Node.js Todo API** with a **PostgreSQL database** on Kubernetes.  
It supports **persistent storage**, **rolling updates**, and **Ingress access**.

---

## 📦 Project Structure

```

k8s/
postgres/postgres-configmap.yaml    # ConfigMap for Postgres
postgres/postgres-secret.yaml       # Secret for Postgres
postgres/postgres-pvc.yaml          # PVC for database persistence
postgres/postgres-deployment.yaml   # Postgres Deployment
postgres/postgres-service.yaml      # Postgres Service
postgres/postgres-seed.yaml         # Job to seed the postgres db

todo-api/todo-configmap.yaml        # ConfigMap for Todo API
todo-api/todo-secret.yaml           # Secret for Todo API
todo-api/todo-deployment.yaml       # Todo API Deployment
todo-api/todo-service.yaml          # Todo API Service
todo-api/todo-ingress.yaml          # Todo API Ingress

````

---

## Prerequisites

- Kubernetes cluster (local or cloud, e.g., Minikube, OrbStack, GKE)
- kubectl configured to access the cluster
- NGINX Ingress Controller installed
- Docker image of the API pushed to a registry

---

## Setup

1. **Create a Namespace**
   ```bash
   kubectl create namespace todo-app
````

2. **Deploy Postgres**

   ```bash
   kubectl apply -n todo-app -f k8s/postgres-config.yaml
   kubectl apply -n todo-app -f k8s/postgres-storage.yaml
   kubectl apply -n todo-app -f k8s/postgres-deployment.yaml
   ```

3. **Deploy Todo API**

   ```bash
   kubectl apply -n todo-app -f k8s/todo-config.yaml
   kubectl apply -n todo-app -f k8s/todo-deployment.yaml
   ```

4. **Verify Resources**

   ```bash
   kubectl get all -n todo-app
   kubectl get pvc -n todo-app
   ```

5. **Access the API**

   * Add the host to `/etc/hosts` (for local clusters):

     ```
     <INGRESS-IP>   todo.local
     ```
   * Test endpoint:

     ```bash
     curl http://todo.local/todos
     ```

---

## Rolling Updates

* The **Todo API Deployment** uses `RollingUpdate`:

  * `maxUnavailable: 0` → no downtime
  * `maxSurge: 1` → one extra pod during updates

Test it:

```bash
kubectl set image deployment/todo-api todo-api=<new-image> -n todo-app
kubectl rollout status deployment/todo-api -n todo-app
```

---

## Test Database Persistence

1. Create a Todo:

   ```bash
   curl -X POST http://todo.local/todos -H "Content-Type: application/json" \
   -d '{"title": "Persistent Test"}'
   ```
2. Delete the Postgres pod:

   ```bash
   kubectl delete pod -l app=postgres -n todo-app
   ```
3. After the pod restarts, check data:

   ```bash
   curl http://todo.local/todos
   ```

---

## Deploy to GCP (GKE)

* add `storageClassName: standard` if deploying on GCP, not required if using Minikube or Orbstack
* Use a **static IP** or **Cloud DNS** for your Ingress host.
* Update `/etc/hosts` locally or configure a DNS record pointing to the GCP LoadBalancer IP.

---