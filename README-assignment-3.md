
## Docker Compose Configuration

### Network Configuration
```yaml
networks:
  goals-network:
    driver: bridge
```
- All services can communicate using container names as hostnames

### Services Configuration

#### 1. MongoDB Service
```yaml
mongodb:
  image: jotaantunes03/mongo:v1
  container_name: goals_mongodb
  restart: unless-stopped
  volumes:
    - mongo_data:/data/db
  networks:
    - goals-network
```
- Uses custom MongoDB image from DockerHub
- Data persistence through named volume `mongo_data`
- Automatic restart policy for reliability
- Connected to the custom network

#### 2. Backend Service
```yaml
backend:
  image: jotaantunes03/goals-backend:v1
  depends_on:
    - mongodb
  ports:
    - "80:80"
  networks:
    - goals-network
  environment:
    - MONGO_URI=mongodb://mongodb:27017/course-goals
```
- Node.js backend exposed on port 80
- Depends on MongoDB service being available
- Environment variable for database connection
- Uses container name `mongodb` for database connection

#### 3. Frontend Service
```yaml
frontend:
  image: jotaantunes03/goals-frontend:v1
  depends_on:
    - backend
  ports:
    - "4000:3000"
  networks:
    - goals-network
  environment:
    - REACT_APP_BACKEND_URL=http://backend:80
```
- React frontend accessible on port 4000
- Maps host port 4000 to container port 3000
- Configured to communicate with backend service
- Environment variable points to backend URL

### Volume Configuration
```yaml
volumes:
  mongo_data:
```
- Named volume for MongoDB data persistence

## Quick Deployment

### Prerequisites
- Docker Desktop installed and running
- No need to build images manually (pre-built images on DockerHub)

### Deployment Steps

1. **Navigate to project directory**
   ```bash
   cd path/to/your/project
   ```

2. **Start the application with Docker Compose**
   ```bash
   docker compose up
   ```

3. **Verify all services are running**
   ```bash
   docker compose ps
   ```
   Expected output:
   ```
   NAME                                 IMAGE                             COMMAND                  SERVICE    CREATED          STATUS         PORTS
   goals_mongodb                        jotaantunes03/mongo:v1            "docker-entrypoint.s…"   mongodb    18 minutes ago   Up 4 seconds   27017/tcp
   multi-01-starting-setup-backend-1    jotaantunes03/goals-backend:v1    "docker-entrypoint.s…"   backend    18 minutes ago   Up 4 seconds   0.0.0.0:80->80/tcp
   multi-01-starting-setup-frontend-1   jotaantunes03/goals-frontend:v1   "docker-entrypoint.s…"   frontend   18 minutes ago   Up 4 seconds   0.0.0.0:4000->3000/tcp
   ``` 
   

4. **Access the application**
   Open your browser and navigate to: **http://localhost:4000**



## Management Commands

### View Logs
```bash
# View all services logs
docker compose logs

# View specific service logs
docker compose logs frontend
docker compose logs backend  
docker compose logs mongodb
```

### Stop Application
```bash
docker compose down
```

### Stop and Remove with Volumes
```bash
docker compose down -v
```

### Restart Services
```bash
docker compose restart
```

### Docker Hub
```
https://hub.docker.com/repositories/jotaantunes03
```

