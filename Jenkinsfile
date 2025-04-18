pipeline {
    agent any

    options {
        skipDefaultCheckout(false)
        timeout(time: 30, unit: 'MINUTES')
    }

    environment {
        IMAGE_NAME = 'recommendation-service'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'git log -1'
            }
        }

        stage('Build') {
            steps {
                script {
                    def startTime = System.currentTimeMillis()
                    sh 'docker build --no-cache -t $IMAGE_NAME .'
                    def duration = (System.currentTimeMillis() - startTime) / 1000
                    echo "Build completed in ${duration} seconds"
                    // Set build duration for later stages
                    env.BUILD_DURATION = duration.toString()
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run pytest inside the container to ensure dependencies are met
                    sh 'docker run --rm $IMAGE_NAME pytest tests/ --junitxml=test-results.xml'
                }
            }
            post {
                always {
                    junit 'test-results.xml'
                }
            }
        }
    }

    post {
        always {
            script {
                def resultIcon = currentBuild.currentResult == 'SUCCESS' ? '✅' : '❌'
                def durationText = env.BUILD_DURATION ?: "unknown"
                currentBuild.description = """
                ${resultIcon} ${currentBuild.currentResult}
                🏗️ Build: ${durationText}s
                📦 Image: ${IMAGE_NAME}
                """.stripIndent().trim()
            }
        }
    }
}
