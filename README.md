# **MERN Stack Deployment with Kubernetes and Helm**  

## **📌 Overview**  
This project sets up a **MERN (MongoDB, Express, React, Node.js) stack** using **Kubernetes** and **Helm**, integrating **CI/CD with Jenkins** for automation. The deployment ensures scalability and security while utilizing Kubernetes best practices.  

---

## **⚡ Prerequisites**  
Before proceeding, ensure the following tools are installed:  

- **Docker** → Containerizes the application.  
- **Kubernetes (Minikube, EKS, GKE, etc.)** → Manages the cluster.  
- **kubectl** → CLI to interact with Kubernetes.  
- **Helm** → Manages Kubernetes applications.  
- **Jenkins** → Automates build and deployment.  
- **Git** → Version control for code management.  

---

## **📂 Project Setup**  
### **1️⃣ Cloning the Repositories**  
The application consists of two separate repositories:  

```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/mern-frontend.git
git clone https://github.com/YOUR_GITHUB_USERNAME/mern-backend.git
```


## **🐳 Building and Pushing Docker Images**  
1. **Frontend**  
  ```bash
   cd mern-frontend
   docker build -t your-dockerhub-username/frontend-app:v1 .
   docker push your-dockerhub-username/frontend-app:v1
   ```
   
2. **Backend**
```bash
cd ../mern-backend
docker build -t your-dockerhub-username/backend-app:v1 .
docker push your-dockerhub-username/backend-app:v1
```
    
## **🚀 Deploying Kubernetes Resources**  
### **2️⃣ Configuring Kubernetes Manifests**  
Create a folder **`k8s/`** and place the following files:  

### **Frontend**  
```bash
kubectl apply -f k8s/frontend-configmap.yaml
kubectl apply -f k8s/frontend-deployment.yaml
kubectl apply -f k8s/frontend-service.yaml
```

### **Backend**
```bash
kubectl apply -f k8s/backend-configmap.yaml
kubectl apply -f k8s/backend-secret.yaml
kubectl apply -f k8s/backend-deployment.yaml
kubectl apply -f k8s/backend-service.yaml
```
### **MongoDB**
```bash
kubectl apply -f k8s/mongo-secret.yaml
kubectl apply -f k8s/mongo-deployment.yaml
```
### **To verify deployments:**
```bash
kubectl get pods
kubectl get services
```

## **🎯 Deploying with Helm**  
1. **Create Helm Chart**  
```bash
helm create mern-chart
```

2. **Modify values.yaml**
```bash
frontend:
  image: "your-dockerhub-username/frontend-app:v1"
  containerPort: 3000
  servicePort: 3000
  nodePort: 32000

backend:
  image: "your-dockerhub-username/backend-app:v1"
  containerPort: 5000
  servicePort: 5000
  environment:
    MONGO_HOST: "mongodb-service"
    MONGO_PORT: "27017"

mongodb:
  image: "mongo:6.0"
  containerPort: 27017
  storage: 5Gi
  secret:
    username: "your-db-username"
    password: "your-db-password"
```

3. **Install Helm Chart**
```bash
helm upgrade --install mern-app ./mern-chart
```

To check the deployment:

```bash
kubectl get pods
kubectl get svc
```

**CI/CD Automation with Jenkins**
Setting Up Jenkins Pipeline
1. Install Plugins:

Docker Pipeline
Kubernetes CLI
GitHub Integration

2. Add DockerHub Credentials in Jenkins:

Navigate to: Manage Jenkins → Manage Credentials
Add a new credential:
ID: dockerhub-credentials
Username: Your DockerHub Username
Password: Your DockerHub Password

3. Jenkinsfile (Pipeline Script)
```bash
pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_REPO = 'your-dockerhub-username'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/YOUR_GITHUB_USERNAME/mern-frontend.git', branch: 'main'
                git url: 'https://github.com/YOUR_GITHUB_USERNAME/mern-backend.git', branch: 'main'
            }
        }
        stage('Pull Existing Images') {
            steps {
                sh "docker pull $DOCKER_REPO/frontend-app:v1"
                sh "docker pull $DOCKER_REPO/backend-app:v1"
            }
        }
        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "$DOCKER_CREDENTIALS_ID", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin'
                    sh "docker push $DOCKER_REPO/frontend-app:v1"
                    sh "docker push $DOCKER_REPO/backend-app:v1"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'helm upgrade --install mern-app ./mern-chart'
            }
        }
    }
}
```

✅ Final Checklist
✔ Frontend & Backend Repos Forked
✔ Docker Images Built & Pushed
✔ Kubernetes Manifests Applied
✔ Helm Chart Configured & Deployed
✔ Jenkins CI/CD Set Up
✔ Application Running Successfully

💡 Summary
This project demonstrates how to deploy a full MERN application in a Kubernetes cluster.
CI/CD automation ensures seamless updates via Jenkins.
Helm simplifies deployment and maintains configurations dynamically.
