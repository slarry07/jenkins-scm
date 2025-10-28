# DevOps CI/CD Pipeline with Jenkins and Docker

## Overview
This project demonstrates a complete CI/CD pipeline using **Jenkins**, **Docker**, and **AWS EC2**.  
The pipeline automates the process of building, testing, and deploying a simple web application (`index.html`) to a container running on an EC2 instance.

---

## Architecture
1. **Source Code Management (SCM)** – GitHub repository hosts the application code and Jenkinsfile.
2. **Jenkins** – Automates build, test, and deployment stages.
3. **Docker** – Containerizes the application.
4. **AWS EC2** – Hosts Jenkins and the deployed container.
5. **Security Group** – Inbound rules allow:
   - Port 22 for SSH
   - Port 8080 for Jenkins
   - Port 8081 for the web app

---

## Prerequisites
- AWS EC2 instance (Ubuntu preferred)
- Docker and Docker Compose installed
- Jenkins installed and running on port 8080
- Git installed
- Security group configured:
  - `TCP 8080` → Jenkins
  - `TCP 8081` → Application
  - `TCP 22` → SSH

---

## Setup Instructions

### 1. Clone the Repository
```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
2. Build Docker Image
docker build -t mywebapp:latest .
3. Run the Container
docker run -d -p 8081:80 mywebapp:latest
Access the app at http://<EC2-Public-IP>:8081

Jenkins Pipeline Setup
Step 1: Configure Jenkins
Install plugins:
Git
Docker Pipeline
Pipeline
Create a new Pipeline Job
Connect Jenkins to your GitHub repo
Step 2: Add Jenkinsfile
The Jenkinsfile defines the CI/CD stages.

Jenkinsfile
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mywebapp:latest"
        CONTAINER_NAME = "mywebapp_container"
        PORT = "8081"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/<your-username>/<your-repo>.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh '''
                    docker ps -q --filter "name=$CONTAINER_NAME" | grep -q . && docker stop $CONTAINER_NAME && docker rm $CONTAINER_NAME || true
                    docker run -d --name $CONTAINER_NAME -p $PORT:80 $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Post-Deployment Verification') {
            steps {
                script {
                    sh 'curl -I http://localhost:$PORT'
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful! Access the app at http://<EC2-Public-IP>:$PORT"
        }
        failure {
            echo "Deployment failed. Check Jenkins logs."
        }
    }
}
Verification
After the pipeline runs:

Open your browser.
Visit http://<EC2-Public-IP>:8081
You should see your index.html page.
Troubleshooting
Port not accessible: Check AWS Security Group inbound rules.
Container not running: Run docker ps -a to inspect logs.
Jenkins build fails: Review Jenkins console output for errors.
Author
Olanrewaju Salami
DevOps Engineer | CI/CD Automation | Cloud Infrastructure


---

This `README.md` and `Jenkinsfile` will let you demonstrate a full CI/CD pipeline deployment using Jenkins and Docker on AWS EC2 with port 8081 exposed, matching the configuration shown on your screen.
