pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = '116488731209.dkr.ecr.ap-south-1.amazonaws.com/carwebsite'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                credentialsId: 'github-creds',
                url: 'https://github.com/monishaDevOps7/carwebsite-jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t carwebsite .'
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag carwebsite:latest $ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin 116488731209.dkr.ecr.ap-south-1.amazonaws.com
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                kubectl set image deployment/carwebsite \
                carwebsite=$ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh 'kubectl rollout status deployment/carwebsite'
            }
        }
    }
}
