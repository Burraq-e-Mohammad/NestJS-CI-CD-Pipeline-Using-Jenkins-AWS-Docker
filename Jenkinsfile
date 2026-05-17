pipeline {
    agent any

    environment {
        CONTAINER_NAME = "nestjs-app"
        IMAGE_NAME = "nestjs-image"
        EMAIL = "burraqemohammad@gmail.com"
        PORT = "3000"
    }

    stages {
        stage('Clone Repo'){
            steps{
                git branch: 'main', url: 'https://github.com/Burraq-e-Mohammad/NestJS-CI-CD-Pipeline-Using-Jenkins-AWS-Docker.git'
            }
        }
        stage('Build Docker Image'){
            steps{
                sh 'docker build -t $IMAGE_NAME .'
            }
        }
        stage('Stop & Remove Previous Container'){
            steps{
                sh '''
                    docker container stop $CONTAINER_NAME || true
                    docker container rm $CONTAINER_NAME || true
                '''
            }
        }
        stage('Docker Container Run'){
            steps{
                sh '''
                    docker run -d -p ${PORT}:${PORT} --name $CONTAINER_NAME $IMAGE_NAME
                '''
            }
        }
        stage('Send Email Notification'){
            steps{
                emailest(
                    subject: "NestJS Application Deployment Status On EC2",
                    body: "Your NestJS Application Has Been Successfully Deployed On http://13.48.43.61:${PORT}/",
                    to: "${EMAIL}"
                )
            }
        }
    }
}