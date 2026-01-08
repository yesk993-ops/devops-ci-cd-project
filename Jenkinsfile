pipeline {
    agent any

    environment {
        IMAGE_NAME = "mydocker3692/newflashapp"
        IMAGE_TAG  = "latest"
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/yesk993-ops/devops-ci-cd-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                  docker build -t $IMAGE_NAME:latest .
                  docker tag $IMAGE_NAME:latest $IMAGE_NAME:$IMAGE_TAG
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push $IMAGE_NAME:latest
                    """
                }
            }
        }

        stage('Deploy Application to Kubernetes') {
            steps {
                sh """
                  kubectl apply -f deployment.yaml
                """
            }
        }

        stage('Expose Application using Service') {
            steps {
                sh """
                  kubectl apply -f service.yml
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Please check logs."
        }
    }
}
