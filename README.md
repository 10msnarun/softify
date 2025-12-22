

---

# MEAN Stack CI/CD Deployment using Docker, Jenkins, and AWS

## Overview

This project demonstrates end-to-end **containerization, CI/CD automation, and cloud deployment** of a full-stack **MEAN (MongoDB, Express, Angular, Node.js)** application.

The goal was to convert a **manual Docker Compose deployment** into an **automated CI/CD pipeline** using **Jenkins**, with deployment on an **AWS EC2 Ubuntu VM**, and traffic routed via **Nginx reverse proxy on port 80**.

---

## Architecture (High Level)

```
GitHub
   ↓ (push)
Jenkins (CI/CD)
   ↓ (SSH)
AWS EC2 (Ubuntu)
   ├── Nginx (Reverse Proxy :80)
   ├── Frontend (Angular → Nginx)
   ├── Backend (Node + Express)
   ├── MongoDB (Docker)
   └── Redis (Docker)
```

---

## Tech Stack

* **Frontend:** Angular
* **Backend:** Node.js + Express
* **Database:** MongoDB (Docker image)
* **Cache:** Redis
* **Containerization:** Docker
* **Orchestration:** Docker Compose
* **CI/CD:** Jenkins (Pipeline as Code)
* **Cloud:** AWS EC2 (Ubuntu)
* **Reverse Proxy:** Nginx

---

## Repository Structure

```
.
├── backend/
│   └── Dockerfile
├── frontend/
│   └── Dockerfile
├── docker-compose.yml
├── Jenkinsfile
└── README.md
```

---

## Dockerization

### Backend Dockerfile

* Multi-stage Node.js (20-alpine)
* Exposes port `8080`
* Runs `node server.js`

### Frontend Dockerfile

* Angular build stage
* Nginx runtime stage
* Serves static files on port `80`

---

## Docker Hub Images

Images were built and pushed to Docker Hub:

* **Backend:** `arun101010/dd-backend:latest`
* **Frontend:** `arun101010/dd-frontend:latest`

> Docker Hub authentication and push were verified successfully.

---

## Cloud Setup (AWS EC2)

* **OS:** Ubuntu
* **Installed:**

  * Docker
  * Docker Compose
  * Git
* Project located at:

  ```
  /home/ubuntu/softify
  ```

The `ubuntu` user was added to the `docker` group to allow non-sudo Docker access.

---

## Docker Compose Setup

The `docker-compose.yml` defines the following services:

* **mongodb** – Official MongoDB image with volume & healthcheck
* **redis** – Redis Alpine image
* **api** – Node/Express backend (port 8080)
* **frontend** – Angular app served via Nginx
* **reverse-proxy** – Nginx routing:

  * `/` → frontend
  * `/api` → backend

All services run in a single isolated Docker network.

---

## CI/CD Pipeline (Jenkins)

### Pipeline Stages

#### 1. Checkout

* Pulls latest code from GitHub (`main` branch)

#### 2. Build Images

* Builds backend and frontend Docker images inside Jenkins
* Ensures Dockerfiles are valid and reproducible

#### 3. Deploy to AWS EC2

* Jenkins SSHs into EC2 using SSH Agent credentials
* Runs:

  ```bash
  git fetch origin main
  git reset --hard origin/main
  docker-compose down
  docker-compose up --build -d
  ```
* Old containers are removed
* New containers are rebuilt and started

---

## Jenkinsfile (Pipeline as Code)

The entire CI/CD logic is defined inside a `Jenkinsfile`, ensuring:

* Repeatability
* Version control
* No manual Jenkins job configuration

---

## Application Access

* **Public URL:**

  ```
  http://<EC2-PUBLIC-IP>/
  ```

* Nginx listens on **port 80**

* No domain or HTTPS configured (not required)

---

## Verification

### Backend Logs

* Server running on port `8080`
* MongoDB connection successful

### Frontend

* Angular UI loads successfully in browser
* Static assets served via Nginx

### Docker

* All containers healthy:

  ```bash
  docker-compose ps
  ```

---

## Screenshots (To Be Included)

The following screenshots are included in the repository/documentation:

* Jenkins pipeline execution (success)
* Docker image build & push logs
* Docker Hub repositories
* Running application in browser
* `docker-compose ps` output
* Nginx reverse proxy configuration

---

## Future Improvements (Production-Grade)

* Pull prebuilt images from Docker Hub instead of rebuilding on EC2
* Use image version tags instead of `latest`
* Add GitHub webhook trigger for Jenkins
* Enable HTTPS with Let’s Encrypt
* Add monitoring (Prometheus / Grafana)

---

## Conclusion

This project successfully demonstrates:

* Dockerizing a full-stack MEAN application
* Orchestrating services using Docker Compose
* Automating build and deployment with Jenkins
* Deploying on AWS with Nginx reverse proxy

The manual deployment process was fully replaced by an automated CI/CD pipeline.

---


