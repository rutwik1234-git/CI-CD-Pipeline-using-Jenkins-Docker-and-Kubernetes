pipeline {
    agent any

    stages {

        stage('Build Docker Images') {
            steps {
                echo '🐳 Building Docker images...'

                sh '''
                    docker build -t rutwik02/frontend:latest ./frontend
                    docker build -t rutwik02/backend:latest ./backend
                '''
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                echo '📤 Pushing Docker images to DockerHub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-cred',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                        docker push rutwik02/frontend:latest
                        docker push rutwik02/backend:latest

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo '🚀 Deploying application to EKS...'

                sh '''
                    aws eks update-kubeconfig --region ap-south-1 --name K8S-Cluster

                    kubectl config current-context

                    kubectl apply -f frontend/deployment.yaml
                    kubectl apply -f backend/deployment.yaml

                    kubectl get pods
                    kubectl get services
                '''
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD Pipeline completed successfully!'
        }

        failure {
            echo '❌ Pipeline failed. Check logs for details.'
        }
    }
}
