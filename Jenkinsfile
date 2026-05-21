pipeline {
    agent any

    environment {
        IMAGE_NAME = "feed-front-img"
        K8S_DEPLOYMENT = "frontend-deployment"
        K8S_CONTAINER = "frontend"
    }

    stages {
        stage('Clone Repo') {
            steps {
                echo 'Code pull ho raha hai GitHub se...'
                checkout scm
            }
        }

        stage('Docker Image Build') {
            steps {
                echo 'Docker image build ho rahi hai...'
                sh '''
                    docker build -t ${IMAGE_NAME}:latest .
                    docker save ${IMAGE_NAME}:latest | k3s ctr images import -
                '''
            }
        }

        stage('Deploy to k3s') {
            steps {
                echo 'k3s pe deploy ho raha hai...'
                sh '''
                    kubectl apply -f k8s/frontend-deployment.yaml
                    kubectl apply -f k8s/ingress.yaml
                    kubectl rollout restart deployment/${K8S_DEPLOYMENT}
                    kubectl rollout status deployment/${K8S_DEPLOYMENT}
                '''
            }
        }
    }

    post {
        success {
            echo 'Frontend successfully deploy ho gaya! ✅'
        }
        failure {
            echo 'Pipeline fail ho gayi ❌'
        }
    }
}
