pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')   // your DockerHub credential ID
        IMAGE_NAME = "pavanambuskar/angular-frontend"       // your image name on DockerHub
        CONTAINER_NAME = "angular-frontend"                 // container name
    }

    stages {
        stage('Login to DockerHub') {
            steps {
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                '''
            }
        }

        stage('Pull Docker Image') {
            steps {
                sh '''
                    echo "📦 Pulling image ${IMAGE_NAME}:latest ..."
                    docker pull ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    echo "🚀 Running container ${CONTAINER_NAME} ..."
                    docker ps -q --filter "name=${CONTAINER_NAME}" | grep -q . && docker rm -f ${CONTAINER_NAME} || true
                    docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Container is up and running! Visit your server's public IP to view the app."
        }
        failure {
            echo "❌ Pipeline failed. Check logs for details."
        }
    }
}
