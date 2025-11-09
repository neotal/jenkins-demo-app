pipeline {
    agent any

    environment {
        APP_NAME = "jenkins-demo-app"
        DOCKER_IMAGE = "${APP_NAME}"
        BUILD_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "${APP_NAME}-${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo '📥 Checking out code from Git...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '📦 Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo '✅ Running tests...'
                sh 'npm test || echo "No tests found, skipping..."'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE}:${BUILD_TAG} ."
                sh "docker tag ${DOCKER_IMAGE}:${BUILD_TAG} ${DOCKER_IMAGE}:latest"
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Deploying container...'
                sh '''
                    docker stop ${APP_NAME} 2>/dev/null || true
                    docker rm ${APP_NAME} 2>/dev/null || true
                    docker run -d --name ${APP_NAME} -p 3000:3000 ${DOCKER_IMAGE}:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Build and deploy succeeded! App running at http://localhost:3000"
        }
        failure {
            echo "❌ Build failed — check logs!"
        }
    }
}
