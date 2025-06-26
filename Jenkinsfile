pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/AdityaGarasangi/Jenkins-CICD-Pipeline-Nodejs.git'
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
                sh 'docker build -t jenkins-p1 .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d --rm -p 3000:3000 --name jenkins-p1 jenkins-p1'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'docker rm -f jenkins-p1 || true'
        }
    }
}
