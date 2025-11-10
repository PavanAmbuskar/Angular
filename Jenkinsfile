pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')   // Jenkins credential ID for DockerHub
        IMAGE_NAME = "pavanambuskar/angular-app"       // Your DockerHub repo
        CONTAINER_NAME = "angular-frontend"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "📦 Cloning repository..."
                git branch: 'main', url: 'https://github.com/PavanAmbuskar/Angular.git'
            }
        }

        stage('Build Angular App') {
            agent {
                docker {
                    image 'node:20-alpine'       // Lightweight Node.js image
                    args '-u root:root'
                }
            }
            steps {
                sh '''
                    echo "⚙️ Building Angular app..."
                    cd angular-frontend
                    npm ci --cache .npm --prefer-offline
                    npx ng build --configuration production --output-path=dist
                    echo "✅ Angular build complete."
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "🐳 Building Docker image..."
                    cd angular-frontend
                    sudo docker build -t ${IMAGE_NAME}:latest .
                    echo "✅ Docker image built successfully."
                '''
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh '''
                    echo "🔑 Logging in to DockerHub..."
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    echo "✅ Login successful."
                '''
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh '''
                    echo "🚀 Pushing image to DockerHub..."
                    docker push ${IMAGE_NAME}:latest
                    echo "✅ Image pushed successfully."
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
                    echo "✅ Application is running on port 80."
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit your server's public IP to view the Angular app."
        }
        failure {
            echo "❌ Pipeline failed. Please check the Jenkins logs."
        }
    }
}
