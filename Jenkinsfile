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
        dir('angular-frontend') {
          bat "npm install"
        }
      }
    }

    stage('Build Angular') {
      steps {
        dir('angular-frontend') {
          bat "npm run build -- --configuration production"
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        dir('angular-frontend') {  // Dockerfile is here
          bat "docker build -t ${IMAGE}:${TAG} ."
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        script {
          docker.withRegistry('', 'dockerhub') {
            docker.image("${IMAGE}:${TAG}").push()
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
        withEnv(["KUBECONFIG=${env.KUBECONFIG}"]) {

          bat "kubectl set image deployment/angular-frontend-deployment angular-frontend=${IMAGE}:${TAG} --record"
          
          bat "kubectl rollout restart deployment/angular-frontend-deployment"
          
          bat "kubectl rollout status deployment/angular-frontend-deployment"
        }
      }
    }
  }
}
