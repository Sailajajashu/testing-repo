pipeline {
    agent any

    environment {
        // Define environment variables
        DOCKER_REGISTRY = "your-docker-registry"
        DOCKER_IMAGE_NAME = "your-image-name"
        DOCKER_IMAGE_TAG = "latest"
        KUBE_NAMESPACE = "your-namespace"
        KUBE_CONFIG = "path-to-kubeconfig" // Path to kubeconfig file for kubectl
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from version control (e.g., Git)
                git 'https://github.com/Sailajajashu/testing-repo.git'
            }
        }

        stage('Maven Build') {
            steps {
                // Build the project using Maven
                sh 'mvn clean package'
            }
        }

        stage('Docker Build and Push') {
            steps {
                // Build Docker image
                script {
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")
                }

                // Push Docker image to the registry
                script {
                    docker.withRegistry('https://${DOCKER_REGISTRY}', 'docker-credentials-id') {
                        docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Deploy to Kubernetes using kubectl
                script {
                    withKubeConfig([credentialsId: 'kube-credentials-id', serverUrl: 'https://your-kubernetes-api-server']) {
                        sh """
                            kubectl apply -f k8s/deployment.yaml -n ${KUBE_NAMESPACE}
                            kubectl apply -f k8s/service.yaml -n ${KUBE_NAMESPACE}
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
