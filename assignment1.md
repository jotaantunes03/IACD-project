# Assignment #1: Build Images and Launch Containers

> **Objective:** Build Docker images and launch individual containers

## 📋 Assignment Overview

This assignment focuses on containerizing each component of the Tracking Goals application by building Docker images and launching containers independently.

## Tasks

1. Create Dockerfiles for Backend and Frontend
2. Build Docker images
3. Pull MongoDB image
4. Launch containers with port mapping

## Architecture

```
Host OS
├── Database Container (MongoDB)
│   └── Port: 27017 → localhost:27017
├── Backend Container (Node.js)
│   └── Port: 80 → localhost:80
└── Frontend Container (React)
    └── Port: 3000 → localhost:3000
```

## Prerequisites

- **Docker Desktop** installed and running
- **Project folder** with complete structure (backend and frontend directories)

## Setup Instructions

### Step 1: Navigate to Project Directory
```bash
cd path/to/your/folder/multi-01-starting-setup
```

### Step 2: Build Docker Images

#### Backend Image
```bash
docker build -t goals-backend ./backend
```

#### Frontend Image
```bash
docker build -t goals-frontend ./frontend
```

#### Database Image (MongoDB)
```bash
docker pull mongo:latest
```

### Step 3: Launch Containers

#### Start MongoDB Container
```bash
docker run -d \
  --name mongodb \
  -p 27017:27017 \
  mongo:latest
```

#### Start Backend Container
```bash
docker run -d \
  --name backend \
  -p 80:80 \
  goals-backend
```

#### Start Frontend Container
```bash
docker run -d \
  --name frontend \
  -p 3000:3000 \
  goals-frontend
```

## ✅ Verification

### Check Running Containers
```bash
docker ps
```

**Expected output:**
```
CONTAINER ID   IMAGE              COMMAND                  STATUS         PORTS
xxxxx          goals-frontend     "docker-entrypoint.s…"   Up 2 minutes   0.0.0.0:3000->3000/tcp
xxxxx          goals-backend      "docker-entrypoint.s…"   Up 3 minutes   0.0.0.0:80->80/tcp
xxxxx          mongo:latest       "docker-entrypoint.s…"   Up 4 minutes   0.0.0.0:27017->27017/tcp
```

### Access the Application
Open your browser and navigate to: **http://localhost:3000**

## 🔧 Management Commands

### View Container Logs
```bash
# Backend logs
docker logs backend

# Frontend logs
docker logs frontend

# MongoDB logs
docker logs mongodb
```

### Stop Containers
```bash
docker stop frontend backend mongodb
```

### Start Containers
```bash
docker start mongodb backend frontend
```

### Remove Containers
```bash
docker stop frontend backend mongodb
docker rm frontend backend mongodb
```

## Known Limitations

- **No networking:** Containers cannot communicate with each other
- **No data persistence:** Data is lost when containers are removed
- **Manual management:** Each container must be managed individually

These limitations will be addressed in **Assignment #2**.

## Notes

- Ensure Docker Desktop is running before executing commands
- Port conflicts: Make sure ports 80, 3000, and 27017 are not in use
- Container names must be unique

---

