pipeline {

    agent any

    options {
        skipDefaultCheckout(true)
    }

    tools {
        nodejs "node24"
    }

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
                dir('angular-frontend') {
                    bat "npm install"
                }
            }
        }

        stage('Build Angular') {
            steps {
                dir('angular-frontend') {
                    bat "npx ng build --configuration production"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('angular-frontend') {
                    bat "docker build -t ${IMAGE}:${TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat """
                        docker login -u %USER% -p %PASS%
                        docker push ${IMAGE}:${TAG}
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat """
                kubectl version --client
                kubectl get pods
                kubectl set image deployment/angular-frontend-deployment angular-frontend=${IMAGE}:${TAG}
                """
            }
        }
    }
}
