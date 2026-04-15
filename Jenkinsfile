pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = "gilma02/runcalc-pro"
        IMAGE_TAG      = "v1.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Get Code') {
            steps {
                checkout scm
                echo "Building tag: ${IMAGE_TAG}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKERHUB_REPO}:${IMAGE_TAG} ."
            }
        }

        stage('Tag Image') {
            steps {
                sh "docker tag ${DOCKERHUB_REPO}:${IMAGE_TAG} ${DOCKERHUB_REPO}:latest"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}
                        docker push ${DOCKERHUB_REPO}:latest
                    """
                }
            }
        }
    }

    post {
        always {
            sh "docker logout"
        }
        success {
            echo "Image ${IMAGE_TAG} pushed successfully!"
        }
        failure {
            echo "CI Pipeline failed. Check logs above."
        }
    }
}
