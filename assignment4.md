# Assignment #4: Kubernetes Deployment

> **Objective:** Deploy multi-container application on Kubernetes cluster

## 📋 Assignment Overview

This assignment focuses on deploying the Tracking Goals application on a Kubernetes cluster using both imperative and declarative approaches. The deployment includes replica configuration and service exposure.

## Tasks

1. Install Kubernetes (Minikube)
2. Deploy using Kubernetes Imperative Approach
   - Database: 1 replica
   - Backend: 2 replicas
   - Frontend: 3 replicas

## Kubernetes Architecture

```
Minikube Cluster
├── Deployments
│   ├── Database (1 replica)
│   ├── Backend (2 replicas)
│   └── Frontend (3 replicas)
└── Services
    ├── database (ClusterIP - Internal)
    ├── backend (NodePort - External)
    └── frontend (NodePort - External)
```

## Prerequisites

- **Ubuntu Server 22.04** with lightweight interface
- **Docker** installed
- **Minikube** installed
- **kubectl** installed
- **DockerHub images:**
  - `jotaantunes03/mongo:latest`
  - `jotaantunes03/goals-backend:latest`
  - `jotaantunes03/goals-frontend:latest`

## Imperative Deployment

### Step 1: Start Minikube Cluster

```bash
# Start Minikube
minikube start

# Verify cluster is running
minikube status

# Check kubectl context
kubectl config current-context
```

Expected output:
```
minikube
```

### Step 2: Deploy Database (MongoDB)

```bash
# Create deployment
kubectl create deployment database --image=jotaantunes03/mongo:latest

# Scale to 1 replica (default, but explicit)
kubectl scale deployment database --replicas=1

# Configure authentication
kubectl set env deployment/database \
  MONGO_INITDB_ROOT_USERNAME=admin \
  MONGO_INITDB_ROOT_PASSWORD=admin

# Expose service (internal only - ClusterIP)
kubectl expose deployment database --port=27017 --target-port=27017
```

**Key points:**
- ClusterIP service (internal communication only)
- Authentication configured via environment variables
- Port 27017 exposed within cluster

### Step 3: Deploy Backend (Node.js)

```bash
# Wait for database to be ready
sleep 30

# Create deployment
kubectl create deployment backend --image=jotaantunes03/goals-backend:latest

# Scale to 2 replicas
kubectl scale deployment backend --replicas=2

# Configure environment variables
kubectl set env deployment/backend \
  MONGO_HOST=database \
  MONGO_USER=admin \
  MONGO_PASSWORD=admin \
  MONGO_DB=course-goals

# Expose service (external - NodePort)
kubectl expose deployment backend --port=80 --target-port=80 --type=NodePort
```

**Key points:**
- 2 replicas for load balancing
- Connects to database using service name `database`
- NodePort service for external access

### Step 4: Deploy Frontend (React)

```bash
# Wait for backend to be ready
sleep 20

# Create deployment
kubectl create deployment frontend --image=jotaantunes03/goals-frontend:latest

# Scale to 3 replicas
kubectl scale deployment frontend --replicas=3

# CRITICAL: Configure backend URL
BACKEND_URL=$(minikube service backend --url)
kubectl set env deployment/frontend REACT_APP_API_URL=${BACKEND_URL}

# Expose service (external - NodePort)
kubectl expose deployment frontend --port=3000 --target-port=3000 --type=NodePort
```

**Key points:**
- 3 replicas for high availability
- Dynamic backend URL configuration using Minikube
- NodePort service for browser access

## Verification

### Step 1: Check Resource Status

```bash
# Check pods
kubectl get pods
```

**Expected output:**
```
NAME                        READY   STATUS    RESTARTS   AGE
backend-xxxxx-xxxxx         1/1     Running   0          5m
backend-xxxxx-xxxxx         1/1     Running   0          5m  
database-xxxxx-xxxxx        1/1     Running   0          6m
frontend-xxxxx-xxxxx        1/1     Running   0          4m
frontend-xxxxx-xxxxx        1/1     Running   0          4m
frontend-xxxxx-xxxxx        1/1     Running   0          4m
```

```bash
# Check services
kubectl get services
```

**Expected output:**
```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
backend      NodePort    10.96.xxx.xxx   <none>        80:3xxxx/TCP     5m
database     ClusterIP   10.96.xxx.xxx   <none>        27017/TCP        6m
frontend     NodePort    10.96.xxx.xxx   <none>        3000:3xxxx/TCP   4m
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          10m
```

```bash
# Check deployments
kubectl get deployments
```

**Expected output:**
```
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
backend    2/2     2            2           5m
database   1/1     1            1           6m
frontend   3/3     3            3           4m
```

### Step 2: Check Connectivity and Logs

```bash
# Check backend logs for MongoDB connection
kubectl logs deployment/backend | grep -i "mongodb\|connected"

# Check frontend logs
kubectl logs deployment/frontend

# View all logs from a specific pod
kubectl logs <pod-name>

# Follow logs in real-time
kubectl logs -f deployment/backend
```

### Step 3: Test the Application

```bash
# Get frontend URL
minikube service frontend --url
```

Open the URL in your browser to access the application.

Alternative:
```bash
# Open directly in default browser
minikube service frontend
```

## Management Commands

### View Resources
```bash
# All resources
kubectl get all

# Detailed pod information
kubectl describe pod <pod-name>

# Detailed service information
kubectl describe service <service-name>

# Detailed deployment information
kubectl describe deployment <deployment-name>
```

### Scale Deployments
```bash
# Scale backend
kubectl scale deployment backend --replicas=4

# Scale frontend
kubectl scale deployment frontend --replicas=5
```

### Update Environment Variables
```bash
kubectl set env deployment/backend NEW_VAR=new_value
```

### Delete Resources
```bash
# Delete specific deployment
kubectl delete deployment frontend

# Delete specific service
kubectl delete service frontend

# Delete all resources
kubectl delete deployment --all
kubectl delete service --all
```

### Restart Deployments
```bash
kubectl rollout restart deployment/backend
kubectl rollout restart deployment/frontend
```

## Cleanup

### Stop Minikube
```bash
minikube stop
```

### Delete Cluster
```bash
minikube delete
```

### Delete All Resources
```bash
kubectl delete deployment --all
kubectl delete service --all
```