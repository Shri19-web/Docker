pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'yourdockerhubusername/webapp'
        CREDENTIALS_ID = 'dockerhub-credentials' // Must be configured in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning repository..."
                git branch: 'main', url: 'https://github.com/Shri19-web/Docker.git'
            }
        }

        stage('Get Git Commit Hash') {
            steps {
                script {
                    COMMIT_HASH = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    env.IMAGE_TAG = "${COMMIT_HASH}"
                    echo "Using Git commit hash as tag: ${IMAGE_TAG}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh """
                        docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} -t ${DOCKER_IMAGE}:latest .
                    """
                }
            }
        }

        stage('Authenticate to Docker Registry') {
            steps {
                echo "Logging into Docker Hub..."
                withCredentials([usernamePassword(credentialsId: "${CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing Docker image to registry..."
                sh """
                    docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                    docker push ${DOCKER_IMAGE}:latest
                """
            }
        }
    }

    post {
        success {
            echo "Docker image pushed successfully: ${DOCKER_IMAGE}:${IMAGE_TAG}"
        }
        failure {
            echo "Jenkins pipeline failed. Check logs for errors."
        }
        always {
            echo "Pipeline execution completed."
        }
    }
}
