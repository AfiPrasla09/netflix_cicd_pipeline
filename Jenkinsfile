pipeline {
    agent any
    
    // Netflix standard: Always check out latest code
    options {
        skipDefaultCheckout(false)  // Force SCM checkout
        timeout(time: 30, unit: 'MINUTES')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Explicit SCM checkout
                sh 'git log -1'  // Verify last commit
            }
        }

        stage('Build') {
            steps {
                script {
                    // Netflix metrics tracking
                    startTime = System.currentTimeMillis()
                    
                    // Real build command
                    sh 'docker build --no-cache -t recommendation-service .'
                    
                    // Record duration
                    duration = (System.currentTimeMillis() - startTime)/1000
                    echo "Build completed in ${duration} seconds"
                }
            }
        }

        stage('Test') {
            steps {
                sh 'pytest tests/ --junitxml=test-results.xml'
            }
            post {
                always {
                    junit 'test-results.xml'  // Netflix test reporting standard
                }
            }
        }
    }

    post {
        always {
            // Netflix-style build summary
            script {
                currentBuild.description = """
                ${currentBuild.currentResult == 'SUCCESS' ? '✅' : '❌'} ${currentBuild.currentResult}
                🏗️ Build: ${duration}s
                📦 Image: recommendation-service
                """
            }
        }
    }
}
