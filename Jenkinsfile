pipeline {
    agent any

    environment {
        IMAGE = "pavanambuskar/angular-frontend"
        TAG = "v${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/PavanAmbuskar/Angular.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat "npm install"
            }
        }

        stage('Build Angular') {
            steps {
                bat "npm run build -- --configuration production"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE}:${TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat """
                minikube kubectl -- set image deployment/angular-frontend-deployment \
                angular-frontend=${IMAGE}:${TAG}
                """
            }
        }
    }
}
