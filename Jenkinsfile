pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')   // DockerHub creds in Jenkins
        IMAGE_NAME = "pavanambuskar/angular-frontend"
        CONTAINER_NAME = "angular-frontend"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/PavanAmbuskar/Angular.git'
            }
        }

        stage('Build Angular App') {
            steps {
                sh '''
                npm install @angular/cli
                npm install
                sh 'npx ng build --configuration production'
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${angular-app}:latest .
                '''
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh '''
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                '''
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh '''
                docker push ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                echo "🚀 Running latest container..."
                sh '''
                # Stop and remove old container if exists
                docker ps -q --filter "name=${CONTAINER_NAME}" | grep -q . && docker rm -f ${CONTAINER_NAME} || true

                # Run new container
                docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit your server's public IP to view the app."
        }
        failure {
            echo "❌ Pipeline failed. Please check the Jenkins logs."
        }
    }
}
