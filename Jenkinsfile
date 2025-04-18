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
                    env.BUILD_DURATION = duration.toString()
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Check if tests directory exists before running
                    def testExists = fileExists('tests')
                    if (testExists) {
                        sh 'docker run --rm $IMAGE_NAME pytest tests/ --junitxml=test-results.xml'
                    } else {
                        echo "⚠️ No tests directory found. Skipping tests."
                    }
                }
            }
            post {
                always {
                    // Only try to archive results if they exist
                    script {
                        if (fileExists('test-results.xml')) {
                            junit 'test-results.xml'
                        } else {
                            echo "⚠️ No test-results.xml found to archive."
                        }
                    }
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
