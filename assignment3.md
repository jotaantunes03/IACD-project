# Assignment #3: Docker Compose Deployment

> **Objective:** Publish images to DockerHub and deploy using Docker Compose

## Assignment Overview

This assignment simplifies the deployment process by using Docker Compose to orchestrate the multi-container application. All images are published to DockerHub for easy distribution and deployment.

## Tasks

1. ✅ Publish containers to DockerHub (backend, frontend, database)
2. ✅ Create `docker-compose.yml` configuration
3. ✅ Deploy multi-container application with single command

## Architecture

```
Host OS
└── Docker Compose
    ├── Network: goals-network
    ├── Volume: mongo_data
    └── Services:
        ├── MongoDB (Internal: 27017)
        ├── Backend (4000:80)
        └── Frontend (80:3000)
```

## Prerequisites

- **Docker Desktop** installed and running
- **DockerHub account** (for publishing images)
- **Completed Assignment #2**

## 🐳 Docker Hub Images

All images are publicly available:
- `jotaantunes03/mongo:latest`
- `jotaantunes03/goals-backend:latest`
- `jotaantunes03/goals-frontend:latest`

[View on Docker Hub](https://hub.docker.com/repositories/jotaantunes03)

## Docker Compose Configuration

Create a `docker-compose.yml` file in the project root:

```yaml
version: '3.8'

services:
  mongodb:
    image: jotaantunes03/mongo:latest
    container_name: goals_mongodb
    restart: unless-stopped
    volumes:
      - mongo_data:/data/db
    networks:
      - goals-network

  backend:
    image: jotaantunes03/goals-backend:latest
    container_name: goals_backend
    restart: unless-stopped
    depends_on:
      - mongodb
    ports:
      - "4000:80"
    networks:
      - goals-network
    environment:
      - MONGO_URI=mongodb://mongodb:27017/course-goals

  frontend:
    image: jotaantunes03/goals-frontend:latest
    container_name: goals_frontend
    restart: unless-stopped
    depends_on:
      - backend
    ports:
      - "80:3000"
    networks:
      - goals-network
    environment:
      - REACT_APP_BACKEND_URL=http://localhost:4000

networks:
  goals-network:
    driver: bridge

volumes:
  mongo_data:
```

## Configuration Breakdown

### Network Configuration
```yaml
networks:
  goals-network:
    driver: bridge
```
- All services communicate using container names as hostnames
- Isolated network for secure inter-container communication

### Service: MongoDB
```yaml
mongodb:
  image: jotaantunes03/mongo:latest
  volumes:
    - mongo_data:/data/db
  networks:
    - goals-network
```
- **Image:** Custom MongoDB image from DockerHub
- **Volume:** Persistent data storage at `/data/db`
- **Restart:** Automatic restart policy (`unless-stopped`)
- **Network:** Connected to `goals-network`

### Service: Backend
```yaml
backend:
  image: jotaantunes03/goals-backend:latest
  depends_on:
    - mongodb
  ports:
    - "4000:80"
  environment:
    - MONGO_URI=mongodb://mongodb:27017/course-goals
```
- **Ports:** Host port `4000` maps to container port `80`
- **Dependencies:** Starts after MongoDB
- **Environment:** Database connection using container name `mongodb`

### Service: Frontend
```yaml
frontend:
  image: jotaantunes03/goals-frontend:latest
  depends_on:
    - backend
  ports:
    - "80:3000"
  environment:
    - REACT_APP_BACKEND_URL=http://localhost:4000
```
- **Ports:** Host port `80` maps to container port `3000`
- **Dependencies:** Starts after backend
- **Environment:** Backend URL for API calls

### Volume Configuration
```yaml
volumes:
  mongo_data:
```
- Named volume for MongoDB data persistence
- Managed by Docker

## Deployment Instructions

### Step 1: Navigate to Project Directory
```bash
cd path/to/your/project
```

### Step 2: Start Application
```bash
docker compose up
```

Run in detached mode (background):
```bash
docker compose up -d
```

### Step 3: Verify Services
```bash
docker compose ps
```

**Expected output:**
```
NAME                                 IMAGE                             STATUS         PORTS
goals_mongodb                        jotaantunes03/mongo:latest            Up 2 minutes   27017/tcp
goals_backend                        jotaantunes03/goals-backend:latest    Up 2 minutes   0.0.0.0:4000->80/tcp
goals_frontend                       jotaantunes03/goals-frontend:latest   Up 2 minutes   0.0.0.0:80->3000/tcp
```

### Step 4: Access Application
Open your browser: **http://localhost:80**

## 🔧 Management Commands

### View All Logs
```bash
docker compose logs
```

### View Specific Service Logs
```bash
docker compose logs frontend
docker compose logs backend
docker compose logs mongodb
```

### Follow Logs in Real-time
```bash
docker compose logs -f
```

### Stop Application
```bash
docker compose down
```

### Stop and Remove Volumes (Data Loss)
```bash
docker compose down -v
```

### Restart Services
```bash
docker compose restart
```

### Restart Specific Service
```bash
docker compose restart backend
```

### Scale Services (if configured)
```bash
docker compose up -d --scale backend=3
```

### Rebuild and Start
```bash
docker compose up --build
```

## Verification Steps

### 1. Check Service Status
```bash
docker compose ps
```
All services should show `Up` status.

### 2. Test Backend Connectivity
```bash
curl http://localhost:4000
```

### 3. Test Frontend
Open browser at `http://localhost` and verify the application loads.

### 4. Verify Data Persistence
1. Create a goal in the application
2. Stop the application: `docker compose down`
3. Start again: `docker compose up -d`
4. Verify the goal persists

### 5. Check Logs for Errors
```bash
docker compose logs | grep -i error
```


## Publishing Images to DockerHub

### Tag Images
```bash
docker tag goals-backend jotaantunes03/goals-backend:latest
docker tag goals-frontend jotaantunes03/goals-frontend:latest
docker tag mongo:latest jotaantunes03/mongo:latest
```

### Push to DockerHub
```bash
docker login
docker push jotaantunes03/goals-backend:latest
docker push jotaantunes03/goals-frontend:latest
docker push jotaantunes03/mongo:latest
```
