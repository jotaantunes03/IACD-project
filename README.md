# Docker Configuration Guide

## Prerequisites
- **Docker Desktop** installed and running
- **Project folder** with complete structure

## Dockerfiles Setup
Place the respective `Dockerfile` in both the `backend/` and `frontend/` directories.

<br>
<br>

## Quick Setup

### 1. Navigate to project directory
```bash
cd path/to/your/folder/multi-01-starting-setup
```

### 2. Build Docker images
```bash
# Backend
docker build -t goals-backend ./backend

# Frontend  
docker build -t goals-frontend ./frontend

# Database
docker pull mongo:latest
```

### 3. Create Docker network
```bash
docker network create goals-network
```

### 4. Run containers
```bash
# MongoDB
docker run -d --name mongodb --network goals-network -p 27017:27017 mongo

# Backend
docker run -d --name backend --network goals-network -p 80:80 goals-backend

# Frontend
docker run -d --name frontend -p 3000:3000 goals-frontend
```

### 5. Verify everything is running
```bash
docker ps
```
Should show 3 containers: frontend, backend and mongodb

### 6. Access the application
Open your browser at: **http://localhost:3000**

---

## Management and Monitoring

### View container logs
```bash
# Backend
docker logs backend

# Frontend
docker logs frontend

# MongoDB
docker logs mongodb
```

### Stop the application
```bash
docker stop frontend backend mongodb
```

### Restart the application
```bash
docker start mongodb backend frontend
```

### Stop and remove everything
```bash
docker stop frontend backend mongodb
docker rm frontend backend mongodb
```