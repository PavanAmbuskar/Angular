pipeline {
  agent any

  environment {
    IMAGE = "pavanambuskar/angular-frontend"
    TAG   = "v${BUILD_NUMBER}"
    KUBECONFIG = "C:\\Users\\Dell\\.kube\\config"
  }

  stages {

    /* ---------------------------------------------------
       STAGE 0: Checkout Code
    --------------------------------------------------- */
    stage('Checkout Code') {
      steps {
        git branch: 'main', url: 'https://github.com/PavanAmbuskar/Angular.git'
      }
    }

    /* ---------------------------------------------------
       STAGE 1: Angular Install + Build
    --------------------------------------------------- */
    stage('Build Angular App') {
      steps {
        dir('angular-frontend') {
          bat "npm install"
          bat "npm run build -- --configuration production"
        }
      }
    }

    /* ---------------------------------------------------
       STAGE 2: Docker Build + Push
    --------------------------------------------------- */
    stage('Build & Push Docker Image') {
      steps {

        dir('angular-frontend') {
          bat "docker build -t ${IMAGE}:${TAG} ."
        }

        withCredentials([usernamePassword(credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {

          bat """
            docker login -u %DOCKER_USER% -p %DOCKER_PASS%
            docker push ${IMAGE}:${TAG}
          """
        }
      }
    }

    /* ---------------------------------------------------
       STAGE 3: Kubernetes Deployment
    --------------------------------------------------- */
    stage('Deploy to Kubernetes') {
      steps {

        withEnv(["KUBECONFIG=${env.KUBECONFIG}"]) {

          dir('angular-frontend') {

            bat "kubectl apply -f deployment.yml"
            bat "kubectl apply -f service.yml"

            bat "kubectl set image deployment/angular-frontend-deployment angular-frontend=${IMAGE}:${TAG} --record"

            bat "kubectl rollout restart deployment/angular-frontend-deployment"

            bat "kubectl rollout status deployment/angular-frontend-deployment"
          }
        }
      }
    }
  }
}
