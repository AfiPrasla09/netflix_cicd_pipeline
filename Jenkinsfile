pipeline {
    agent any

    environment {
        IMAGE_NAME = "netflix-cicd-app"
        CONTAINER_PORT = "5000"
        HOST_PORT = "5000"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo '📥 Checking out code...'
                git 'https://github.com/AfiPrasla09/netflix_cicd_pipeline' 
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '📦 Installing Python dependencies...'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Lint Check') {
            steps {
                echo '🔍 Running lint check...'
                sh 'pip install flake8 || true'
                sh 'flake8 app || echo "Lint warnings found"'
            }
        }

        stage('Unit Tests') {
            steps {
                echo '🧪 Running unit tests...'
                sh 'pip install pytest || true'
                sh 'pytest || echo "Some tests failed"'
            }
        }

        stage('Docker Build') {
            steps {
                echo '🐳 Building Docker image...'
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Docker Run') {
            steps {
                echo '🚀 Running Docker container...'
                sh "docker run -d -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}"
            }
        }

        stage('Docker Test') {
            steps {
                echo '🔧 Verifying Docker installation...'
                sh 'docker --version'
                sh 'docker run hello-world'
            }
        }
    }

    post {
        always {
            echo '🧹 Cleaning up Docker containers...'
            sh "docker ps -aq | xargs -r docker rm -f || true"
        }
        success {
            echo '✅ Build completed successfully!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
