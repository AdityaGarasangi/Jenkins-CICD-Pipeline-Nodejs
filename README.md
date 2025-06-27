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

##Dockerfile (Used to Build App Image)
```Dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000
CMD ["npm", "start"]
```

---

## Jenkinsfile (Pipeline Logic)
```Jenkinsfile
pipeline {
    agent any

    environment {
        DOCKER_HOST = 'tcp://host.docker.internal:2375'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-username/your-node-repo.git'
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
            // Uncomment below line for auto cleanup
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

## Author
Aditya Garasangi
Cloud & DevOps Enthusiast
