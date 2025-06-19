pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'a7i0' // Docker registry URL
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials') // Jenkins credentials ID for Docker registry
        KUBECONFIG = credentials('kubeconfig') // Replace with your kubeconfig credentials ID
        HELM_CHART_DIR = 'helm-chart/learner-report'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('learner-report-cs/frontend') {
                    script {
                        docker.build("${env.DOCKER_REGISTRY}/learner-report-frontend:${env.BUILD_ID}").push()
                    }
                }
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('learner-report-cs/backend') {
                    script {
                        docker.build("${env.DOCKER_REGISTRY}/learner-report-backend:${env.BUILD_ID}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                        helm upgrade --install learner-report ${env.HELM_CHART_DIR} \
                            --set image.frontend.repository=${env.DOCKER_REGISTRY}/learner-report-frontend \
                            --set image.frontend.tag=${env.BUILD_ID} \
                            --set image.backend.repository=${env.DOCKER_REGISTRY}/learner-report-backend \
                            --set image.backend.tag=${env.BUILD_ID} \
                            --set mongodb.database=learner-report
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
