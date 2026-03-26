# Banking Application - DevOps Assignment 1

A multi-tier banking application deployed on Kubernetes (Minikube).

## Tech Stack
- **Frontend**: Node.js (Express + EJS) - Port 3000
- **Backend**: Java 17 + Spring Boot 3 + Spring Data JPA - Port 8080
- **Database**: PostgreSQL 15 - Port 5432
- **Orchestration**: Kubernetes (Minikube)

## Architecture
```
Browser → Frontend (NodePort :30080) → Backend (ClusterIP :8080) → PostgreSQL (ClusterIP :5432)
```

## Prerequisites
- Docker Desktop
- Minikube
- kubectl
- Docker Hub account

## Quick Start

### 1. Start Minikube
```bash
minikube start --driver=docker --cpus=2 --memory=4096
```

### 2. Build & Push Docker Images
```bash
# Build images
docker build -t YOUR_USERNAME/banking-backend:v1 ./backend
docker build -t YOUR_USERNAME/banking-frontend:v1 ./frontend

# Push to Docker Hub
docker login
docker push YOUR_USERNAME/banking-backend:v1
docker push YOUR_USERNAME/banking-frontend:v1
```

### 3. Update K8s Manifests
Replace `YOUR_USERNAME` in these files with your Docker Hub username:
- `k8s/backend-deployment.yaml`
- `k8s/frontend-deployment.yaml`

### 4. Deploy to Kubernetes
```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/postgres-secret.yaml
kubectl apply -f k8s/backend-configmap.yaml
kubectl apply -f k8s/frontend-configmap.yaml
kubectl apply -f k8s/postgres-pvc.yaml
kubectl apply -f k8s/postgres-deployment.yaml
kubectl apply -f k8s/postgres-service.yaml
kubectl wait --for=condition=ready pod -l app=postgres -n banking-app --timeout=120s
kubectl apply -f k8s/backend-deployment.yaml
kubectl apply -f k8s/backend-service.yaml
kubectl wait --for=condition=ready pod -l app=backend -n banking-app --timeout=180s
kubectl apply -f k8s/frontend-deployment.yaml
kubectl apply -f k8s/frontend-service.yaml
```

### 5. Access the Application
```bash
minikube service frontend -n banking-app
```

## Cleanup
```bash
kubectl delete namespace banking-app
minikube stop
```
