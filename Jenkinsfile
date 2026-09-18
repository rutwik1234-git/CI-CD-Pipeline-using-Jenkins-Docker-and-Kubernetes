pipeline { 
    agent any

environment {
        DOCKERHUB_CREDENTIALS = 'a0fc5943-0bd4-47cf-ad98-fa964aa7a683'
        DOCKERHUB_REPO = "sumitdorugade"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo '📦 Cloning repository...'
                git branch: 'main',
                url: 'https://github.com/Sumit19D/CI-CD-Pipeline-using-Jenkins-Docker-and-Kubernetes.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    echo '🐳 Building Docker images...'
                    sh """
                        docker build -t rutwik02/frontend:latest ./frontend
                        docker build -t rutwik02/backend:latest ./backend
                    """
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                script {
                    echo '📤 Pushing Docker images to DockerHub...'
                    withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CREDENTIALS', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh """
                            echo "$PASS" | docker login -u "$USER" --password-stdin
                            docker push rutwik02/frontend:latest
                            docker push rutwik02/backend:latest
                            docker logout
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
               script {
                echo '🚀 Deploying application to Kubernetes...'
                
                sh """
                    kubectl apply -f frontend/deployment.yaml --validate=false
                    kubectl apply -f frontend/service.yaml --validate=false
                    kubectl apply -f backend/deployment.yaml --validate=false
                    kubectl apply -f backend/service.yaml --validate=false
                """
            }
        }
    }
}


    post {
        success {
            echo "✅ Pipeline completed successfully.!!!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs for details."
        }
    }
}
