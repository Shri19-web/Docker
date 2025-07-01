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
                }
                echo "Using Git commit hash as tag: ${latest}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh """
                        docker build -t ${my_ubuntu}:${latest} -t ${my_ubuntu}:latest .
                    """
                }
            }
        }

        stage('Authenticate to Docker Registry') {
            steps {
                echo "Logging into Docker Hub..."
                withCredentials([usernamePassword(credentialsId: "${CREDENTIALS_ID}", usernameVariable: '131924', passwordVariable: '123443321')]) {
                    sh 'echo "$123443321" | docker login -u "$131924" --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing Docker image to registry..."
                sh """
                    docker push ${my_ubuntu}:${latest}
                    docker push ${my_ubuntu}:latest
                """
            }
        }
    }

    post {
        success {
            echo "Docker image pushed successfully: ${my_ubuntu}:${latest}"
        }
        failure {
            echo "Jenkins pipeline failed. Check logs for errors."
        }
        always {
            echo "Pipeline execution completed."
        }
    }
}
