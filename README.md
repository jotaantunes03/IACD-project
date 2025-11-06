# Tracking Goals Web Application

> **Course:** Advanced Infrastructures for Data Science  
> **Program:** MIACD  
> **Academic Year:** 2025/2026  
> **Institution:** University of Coimbra

## Project Overview

A multi-container web application that allows users to manage their goals (create, view, and delete). This project demonstrates containerization, orchestration, and deployment using Docker and Kubernetes.

### Application Architecture

```
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│   Frontend  │─────▶│   Backend   │─────▶│  Database   │
│   (React)   │      │  (Node.js)  │      │  (MongoDB)  │
└─────────────┘      └─────────────┘      └─────────────┘
```

**Technology Stack:**
- **Frontend:** React (port 3000)
- **Backend:** Node.js (port 80)
- **Database:** MongoDB (port 27017)



## Project Assignments

### [Assignment #1: Build Images and Launch Containers](./assignment-1/README.md)
- Build Docker images for each component
- Launch individual containers
- Basic container management

### [Assignment #2: Container Connectivity & Data Persistence](./assignment-2/README.md)
- Configure Docker networking
- Connect backend to database
- Connect frontend to backend
- Implement data persistence with volumes

### [Assignment #3: Docker Compose Deployment](./assignment-3/README.md)
- Publish images to DockerHub
- Deploy multi-container app with Docker Compose
- Simplified orchestration and management

### [Assignment #4: Kubernetes Deployment](./assignment-4/README.md)
- Deploy on Kubernetes (Minikube)
- Imperative approach with kubectl commands
- Declarative approach with YAML manifests
- Configure replicas: Database (1), Backend (2), Frontend (3)

## Quick Start

### Prerequisites
- Docker Desktop installed
- Kubernetes (Minikube) installed (for Assignment #4)
- Git installed

### Clone Repository
```bash
git clone 
cd tracking-goals-app
```

### Run with Docker Compose (Recommended)
```bash
docker compose up
```
Access the application at: **http://localhost:4000**

## Docker Hub Images

All images are publicly available on Docker Hub:
- `jotaantunes03/mongo:latest`
- `jotaantunes03/goals-backend:latest`
- `jotaantunes03/goals-frontend:latest`

[View on Docker Hub](https://hub.docker.com/repositories/jotaantunes03)

## Documentation

Each assignment folder contains detailed documentation:
- Step-by-step setup instructions
- Configuration explanations
- Verification procedures
- Management commands

## Author

**João Antunes & Gabriel Pinto**  
Master's degree in Artificial Intelligence and Data Science, University of Coimbra


**Instructor:** Pedro Neves (pedroneves@dei.uc.pt)