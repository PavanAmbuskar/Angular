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
        stage('Check User') {
            steps {
                bat "whoami"
                bat "echo %USERPROFILE%"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withEnv(["KUBECONFIG=C:\\Users\\Dell\\.kube\\config"]) {
                    bat "echo Using Kubeconfig: %KUBECONFIG%"
                    bat "kubectl config view"
                    bat "kubectl get nodes"
                    bat "kubectl get pods"
                    bat "kubectl set image deployment/angular-frontend-deployment angular-frontend=${IMAGE}:${TAG}"
                }
            }
        }
    }
}

// pipeline {
//     agent any
//     stages {
//         stage('Test K8s') {
//             steps {
//                 withEnv(["KUBECONFIG=C:\\Users\\Dell\\.kube\\config"]) {
//                     bat "kubectl config current-context"
//                     bat "kubectl get nodes"
//                     bat "kubectl get pods"
//                 }
//             }
//         }
//     }
// }
