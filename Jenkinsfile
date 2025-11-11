pipeline {
    agent any

    environment {
        IMAGE_NAME = "pavanambuskar/angular-frontend"
        CONTAINER_NAME = "angular-container"
        APP_PORT = "80"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "🐳 Building Docker image..."
                    docker build -t $IMAGE_NAME:latest .
                    echo "✅ Docker image built successfully."
                '''
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh '''
                        echo "🔐 Logging in to DockerHub..."
                        echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                        echo "✅ Logged in to DockerHub."
                    '''
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh '''
                    echo "📤 Pushing image to DockerHub..."
                    docker push $IMAGE_NAME:latest
                    echo "✅ Image pushed successfully."
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    echo "🚀 Running container..."
                    docker rm -f $CONTAINER_NAME || true
                    docker run -d --name $CONTAINER_NAME -p $APP_PORT:80 $IMAGE_NAME:latest
                    echo "✅ Container running at port $APP_PORT"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs for details.'
        }
    }
}
