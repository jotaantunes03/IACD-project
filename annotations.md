## **Critical Issue: Container Networking & Solution**

### **Problem Identified:**
- Backend container tried connecting to MongoDB using `localhost:27017`
- In Docker, each container has isolated networking - `localhost` refers to the container itself, not the host machine
- Result: `ERROR` - backend couldn't locate MongoDB

### **Network Solution Implementation**
**Purpose:** Enable inter-container communication
```bash
docker network create goals-network
```
**Why:** Docker networks allow containers to communicate using container names as hostnames

### **Code Modification - app.js**
**Critical Change:**
```javascript
// BEFORE:
mongoose.connect('mongodb://localhost:27017/course-goals')

// AFTER:
mongoose.connect('mongodb://mongodb:27017/course-goals')
```
**Why:** 
- `mongodb` hostname resolves to the MongoDB container's IP within the Docker network
- Enables backend to locate and connect to the database container

### **Container Deployment with Networking**
```bash
docker run -d --name mongodb --network goals-network -p 27017:27017 mongo
docker run -d --name backend --network goals-network -p 80:80 goals-backend
docker run -d --name frontend -p 3000:3000 goals-frontend
```
**Network Strategy:**
- Backend and MongoDB share `goals-network` for internal communication
- Frontend connects to backend via host port mapping
- Each service accessible through designated ports