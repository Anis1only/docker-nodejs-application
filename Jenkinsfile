pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = "anismullani/docker-nodejs-application" // Docker Hub username and app name
        DOCKER_REGISTRY = "docker.io"
        DOCKER_CREDENTIALS = "docker-credentials" // Make sure this matches the credentials ID in Jenkins
        NODE_VERSION = "14-alpine"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the repository
                    checkout scm
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    echo "Building Docker image..."
                    sh """
                    docker build -t ${DOCKER_IMAGE_NAME}:latest .
                    """
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub using Jenkins credentials
                    echo "Logging into Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh """
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        """
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    echo "Pushing Docker image to Docker Hub..."
                    sh """
                    docker push ${DOCKER_IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Clean Up') {
            steps {
                script {
                    // Clean up local Docker images to save space on the Jenkins agent
                    echo "Cleaning up Docker images..."
                    sh """
                    docker rmi ${DOCKER_IMAGE_NAME}:latest
                    """
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker resources in case of failure
            echo "Cleaning up Docker resources..."
            sh """
            docker system prune -f
            """
        }
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Check the logs for errors."
        }
    }
}
