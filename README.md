# Todo API Microservice on GCP Kubernetes

## Features
- Node.js Express API with CRUD for Todo items
- PostgreSQL database
- Containerized with Docker
- Kubernetes deployment with rolling updates
- ConfigMap and Secret for configuration management
- NGINX Ingress Controller for external access

## Setup Instructions

### 1. Build and Push Docker Image
```sh
cd src
# Build Docker image
# docker build -t <yourdockerhub/YOUR_DOCKER_IMAGE> .
# Push to Docker Hub
# docker push <yourdockerhub/YOUR_DOCKER_IMAGE>
```

### 2. Update Kubernetes Manifests
- Replace `<YOUR_DOCKER_IMAGE>` in `k8s/todo-deployment.yaml` with your image name (e.g., `yourdockerhub/todo-api:latest`)
- Replace `<YOUR_DOMAIN>` in `k8s/todo-ingress.yaml` with your domain or use a public IP

### 3. Deploy PostgreSQL
```sh
kubectl apply -f k8s/postgres-secret.yaml
kubectl apply -f k8s/postgres-deployment.yaml
```

### 4. Deploy Todo API
```sh
kubectl apply -f k8s/todo-configmap.yaml
kubectl apply -f k8s/todo-secret.yaml
kubectl apply -f k8s/todo-deployment.yaml
kubectl apply -f k8s/todo-service.yaml
```

### 5. Deploy NGINX Ingress Controller

```sh
# Install NGINX Ingress Controller (recommended)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/cloud/deploy.yaml

# Deploy your Ingress resource
kubectl apply -f k8s/todo-ingress.yaml
```

### 6. Access the API
- Use the domain or external IP from the Ingress service to access the API endpoints.

## Security & Best Practices
- Store sensitive data in Kubernetes Secrets
- Use ConfigMaps for non-sensitive config
- Use rolling updates for zero downtime
- Keep dependencies up to date

## API Endpoints
## Example curl Commands

Assuming your API is accessible at `http://<YOUR_DOMAIN_OR_IP>/`:

### List all todos
```sh
curl http://<YOUR_DOMAIN_OR_IP>/todos
```

### Create a new todo
```sh
curl -X POST http://<YOUR_DOMAIN_OR_IP>/todos \
  -H "Content-Type: application/json" \
  -d '{"title": "Buy milk"}'
```

### Get a todo by ID
```sh
curl http://<YOUR_DOMAIN_OR_IP>/todos/1
```

### Update a todo
```sh
curl -X PUT http://<YOUR_DOMAIN_OR_IP>/todos/1 \
  -H "Content-Type: application/json" \
  -d '{"completed": true}'
```

### Delete a todo
```sh
curl -X DELETE http://<YOUR_DOMAIN_OR_IP>/todos/1
```
---
