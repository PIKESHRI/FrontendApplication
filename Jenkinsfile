pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = 'your-account-id.dkr.ecr.us-east-1.amazonaws.com/frontend-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/your-org/frontend-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${ECR_REPO}:latest")
                }
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                    aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                """
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    dockerImage.push()
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh """
                    aws eks update-kubeconfig --region $AWS_REGION --name demo-eks
                    kubectl apply -f k8s-deployment.yaml
                """
            }
        }
    }
}
