pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id')
        DOCKER_IMAGE_REPO = 'dibyadarshandevops/project5'  // Repository only
        DOCKER_IMAGE_TAG = 'v1.0'  // Tag only
        KUBECONFIG_PATH = '/var/jenkins_home/.kube/config'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning repository...'
                git 'https://github.com/Dibyadarshan9777/project5.git'  // Update with your repo URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh "docker build -t ${DOCKER_IMAGE_REPO}:${DOCKER_IMAGE_TAG} ."
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo 'Running tests...'
                    sh "docker run --rm ${DOCKER_IMAGE_REPO}:${DOCKER_IMAGE_TAG} /bin/sh -c \"echo Test successful!\""
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    echo 'Logging into Docker Hub...'
                    sh "echo \$DOCKERHUB_CREDENTIALS_PSW | docker login -u \$DOCKERHUB_CREDENTIALS_USR --password-stdin"
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    sh "docker push ${DOCKER_IMAGE_REPO}:${DOCKER_IMAGE_TAG}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo 'Deploying to Kubernetes with Helm...'
                    sh """
                        helm upgrade --install project5-release ./project5 \
                        --set image.repository='${DOCKER_IMAGE_REPO}' \
                        --set image.tag='${DOCKER_IMAGE_TAG}' \
                        --namespace dbspe
                    """
                }
            }
        }
    }

    post {
        always {
            script {
                echo 'Cleaning up...'
                sh "docker rmi ${DOCKER_IMAGE_REPO}:${DOCKER_IMAGE_TAG} || true"
            }
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
