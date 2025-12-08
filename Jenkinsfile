pipeline {
    agent any

    environment {
        IMAGE = "pavanambuskar/angular-frontend"
        TAG   = "v${BUILD_NUMBER}"
        KUBECONFIG = "C:\\Users\\Dell\\.kube\\config"
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

        stage('Apply Kubernetes Manifests') {
            steps {
                bat "kubectl apply -f deployment.yml"
                bat "kubectl apply -f service.yml"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withEnv(["KUBECONFIG=C:\\Users\\Dell\\.kube\\config"]) {
                    bat "kubectl set image deployment/angular-frontend-deployment angular-frontend=${IMAGE}:${TAG} --record"
                    bat "kubectl rollout restart deployment/angular-frontend-deployment"
                    bat "kubectl rollout status deployment/angular-frontend-deployment"
                }
            }
        }
    }
}
