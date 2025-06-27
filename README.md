# Jenkins CI/CD Pipeline for Node.js App with Docker

This project demonstrates a complete CI/CD pipeline using **Jenkins** to build, test, and deploy a **Node.js** application inside a **Docker container**.

The Jenkins pipeline:
- Pulls code from a GitHub repository
- Installs Node.js dependencies
- Runs tests
- Builds a Docker image
- Runs the application in a container

---

## 📁 Project Structure
```pgsql
.
├── node_modules/
├── Dockerfile
├── index.html
├── index.js
├── Jenkinsfile
├── package-lock.json
└── package.json
```


---

## 🚀 Prerequisites

- Docker Desktop (with exposed Docker daemon on `tcp://localhost:2375`)
- Jenkins running in Docker with:
  - Docker CLI installed
  - Node.js installed (via plugin or Dockerfile)
- Jenkins plugins:
  - [Pipeline](https://plugins.jenkins.io/workflow-aggregator/)
  - [Git](https://plugins.jenkins.io/git/)
  - [Docker Pipeline](https://plugins.jenkins.io/docker-workflow/)
  - [NodeJS](https://plugins.jenkins.io/nodejs/)

---

## ⚙️ Jenkins Setup

### Start Jenkins container:

```powershell
docker build -t jenkins-docker .
docker run -d `
  --name jenkins-p1 `
  -p 8080:8080 `
  -p 50000:50000 `
  -v D:\jenkins_home:/var/jenkins_home `
  -e DOCKER_HOST=tcp://host.docker.internal:2375 `
  jenkins-docker
```
Ensure Docker daemon is exposed:
Docker Desktop → Settings → General → ✅ "Expose daemon on tcp://localhost:2375 without TLS"

---

## Dockerfile (Used to Build App Image)
```Dockerfile
FROM jenkins/jenkins:lts

USER root

# Install Docker CLI
RUN apt-get update && \
    apt-get install -y docker.io curl gnupg && \
    curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && \
    apt-get install -y nodejs

USER jenkins
```

---

## Jenkinsfile (Pipeline Logic)
```Jenkinsfile
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                 git branch: 'main', url: 'https://github.com/AdityaGarasangi/Jenkins-CICD-Pipeline-Nodejs.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jenkins-node-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 3000:3000 --name jenkins-node-app jenkins-node-app'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
           // sh 'docker rm -f jenkins-node-app || true'
        }
    }
}
```
---

##  Verify the Deployment
After a successful pipeline run:
  * Visit: http://localhost:3000
  * You should see the running Node.js application.


## Notes
 * If you don’t have test scripts, replace "test" script in package.json with:
```json
"test": "echo \"Skipping tests...\" && exit 0"
```

 * Ensure your app listens on 0.0.0.0:
```js
app.listen(3000, '0.0.0.0', () => console.log('Server running'));
```

## Cleanup
To stop and remove the container manually:
```bash
docker stop jenkins-node-app
docker rm jenkins-node-app
```

---
## 📸 Screenshot

### 🧩 Jenkins Dashboard  
Jenkins dashboard showing the list of jobs and system status.  
![Jenkins Dashboard](screenshots/dashboard.png)

---

### 📊 Pipeline Stages  
All pipeline stages from Checkout to Deployment shown as successful.  
![Pipeline Stages](screenshots/pipeline-stages.png)

---

### 📦 Project Build Status  
Pipeline status summary and job history in Jenkins UI.  
![Project Status](screenshots/project-status.png)

---

### 🐳 Docker Desktop Settings  
Docker daemon exposed via TCP to allow Jenkins container to run Docker commands.  
![Docker Dashboard](screenshots/docker-dashboard.png)

---

### 🧾 Docker Container Running  
Docker container running the Node.js app on port 3000.  
![Docker ps Output](screenshots/docker-ps.png)

---

### 🌐 Node.js App in Browser  
Node.js application successfully deployed and running at http://localhost:3000.  
![Node.js App](screenshots/node-app.png)

---

## Author
Aditya Garasangi
Cloud & DevOps Enthusiast
