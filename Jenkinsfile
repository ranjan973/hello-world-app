pipeline {
    agent any

    environment {
        IMAGE_NAME = "hello-world-java-app"
        CONTAINER_NAME = "hello-world-java-container"
        HOST_PORT = "8082"
        CONTAINER_PORT = "8080"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Build Java Application') {
            steps {
                echo "Building Java application using Maven..."
                sh 'mvn clean package'
            }
        }

        stage('Verify WAR File') {
            steps {
                echo "Checking generated WAR file..."
                sh 'ls -lh webapp/target/*.war'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh '''
                    docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Stop Existing Container') {
            steps {
                echo "Stopping existing container if running..."
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Deploy Docker Container') {
            steps {
                echo "Deploying application as Docker container..."
                sh '''
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p ${HOST_PORT}:${CONTAINER_PORT} \
                    ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Validate Container') {
            steps {
                echo "Checking running container..."
                sh '''
                    docker ps | grep ${CONTAINER_NAME}
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully. Application deployed on port ${HOST_PORT}."
        }

        failure {
            echo "Pipeline failed. Please check console logs."
        }
    }
}
