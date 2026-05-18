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
                    docker tag $IMAGE_NAME ${IMAGE_NAME}:backup || true
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
    }

    post {
        success {
            emailext(
                subject: "SUCCESS: NestJS Deployment [Build #${env.BUILD_NUMBER}]",
                body: "Your NestJS Application has been successfully deployed and is live at http://13.51.194.31:${PORT}/",
                to: "${EMAIL}"
            )
        }
        failure {
            echo "Pipeline failed at stage: ${env.STAGE_NAME}. Initiating recovery..."
            
            sh '''
                docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME}_recovery ${IMAGE_NAME}:backup || echo "Rollback image not found."
            '''
            emailext(
                subject: "🚨 CRITICAL FAILURE: NestJS Deployment [Build #${env.BUILD_NUMBER}]",
                body: """The deployment failed during the '${env.STAGE_NAME}' stage. 
                         The system attempted a rollback to the previous stable image.
                         Check Jenkins Logs: ${env.BUILD_URL}""",
                to: "${EMAIL}"
            )
        }
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
    }
}
