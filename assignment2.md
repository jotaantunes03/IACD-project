# Assignment #2: Container Connectivity & Data Persistence

> **Due Date:** October 3, 2024  
> **Objective:** Configure container networking and implement data persistence

## Assignment Overview

This assignment enhances the containerized application by establishing proper network communication between containers and implementing data persistence using Docker volumes.

## Tasks

1. ✅ Connect backend component with database
2. ✅ Connect frontend component with backend
3. ✅ Persist MongoDB data (`/data/db`)
4. ✅ Persist Node.js logs folder

## Architecture Evolution

### Before (Assignment #1)
```
Host OS
├── Database Container → localhost:27017
├── Backend Container → localhost:80
└── Frontend Container → localhost:3000
    (No inter-container communication)
```

### After (Assignment #2)
```
Host OS
└── Docker Network (goals-network)
    ├── Database Container
    │   ├── Internal: 27017 (no port mapping)
    │   └── Volume: mongo_data → /data/db
    ├── Backend Container
    │   ├── Internal: 80 → localhost:4000
    │   └── Volume: logs_data → /logs
    └── Frontend Container
        └── External: 3000 → localhost:80
```

## Prerequisites

- **Docker Desktop** installed and running
- **Completed Assignment #1** (Docker images built)

## Setup Instructions

### Step 1: Navigate to Project Directory
```bash
cd path/to/your/folder/multi-01-starting-setup
```

### Step 2: Create Docker Network
```bash
docker network create goals-network
```

Verify network creation:
```bash
docker network ls
```

### Step 3: Create Docker Volumes
```bash
# MongoDB data volume
docker volume create mongo_data

# Backend logs volume
docker volume create logs_data
```

Verify volumes:
```bash
docker volume ls
```

### Step 4: Launch Containers with Networking and Persistence

#### Start MongoDB Container
```bash
docker run -d \
  --name mongodb \
  --network goals-network \
  -v mongo_data:/data/db \
  mongo:latest
```

**Key changes:**
- Connected to `goals-network`
- Volume mounted for data persistence
- **No port mapping** (internal communication only)

#### Start Backend Container
```bash
docker run -d \
  --name backend \
  --network goals-network \
  -p 4000:80 \
  -v logs_data:/app/logs \
  -e MONGO_URI=mongodb://mongodb:27017/course-goals \
  goals-backend
```

**Key changes:**
- Connected to `goals-network`
- Port mapped to `4000:80` (changed from 80:80)
- Volume mounted for logs persistence
- Environment variable for MongoDB connection (uses container name `mongodb`)

#### Start Frontend Container
```bash
docker run -d \
  --name frontend \
  --network goals-network \
  -p 80:3000 \
  -e REACT_APP_BACKEND_URL=http://localhost:4000 \
  goals-frontend
```

**Key changes:**
- Connected to `goals-network`
- Port mapped to `80:3000`
- Environment variable for backend connection

## Verification

### Check Running Containers
```bash
docker ps
```

**Expected output:**
```
CONTAINER ID   IMAGE              PORTS                    NAMES
xxxxx          goals-frontend     0.0.0.0:80->3000/tcp     frontend
xxxxx          goals-backend      0.0.0.0:4000->80/tcp     backend
xxxxx          mongo:latest       27017/tcp                mongodb
```

### Check Network Connections
```bash
docker network inspect goals-network
```

### Check Volumes
```bash
docker volume inspect mongo_data
docker volume inspect logs_data
```

### Test Application
1. Open browser: **http://localhost:80**
2. Create a new goal
3. Verify it's saved in the database
4. Restart containers and verify data persists

## Management Commands

### View Container Logs
```bash
# Backend logs
docker logs backend

# Frontend logs
docker logs frontend

# MongoDB logs
docker logs mongodb

# Follow logs in real-time
docker logs -f backend
```

### Stop Application
```bash
docker stop frontend backend mongodb
```

### Restart Application
```bash
docker start mongodb backend frontend
```

### Complete Cleanup
```bash
# Stop and remove containers
docker stop frontend backend mongodb
docker rm frontend backend mongodb

# Remove network
docker network rm goals-network

# Remove volumes (DATA LOSS)
docker volume rm mongo_data logs_data
```

