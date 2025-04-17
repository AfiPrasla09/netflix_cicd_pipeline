pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/your-username/deloitte-ci-cd-demo.git'
      }
    }
    stage('Install') {
      steps {
        sh 'pip install -r requirements.txt'
      }
    }
    stage('Test') {
      steps {
        echo 'No tests yet - simulated Deloitte workflow.'
      }
    }
    stage('Docker Build') {
      steps {
        sh 'docker build -t deloitte-demo-app .'
      }
    }
    stage('Deploy') {
      steps {
        sh 'docker run -d -p 5000:5000 deloitte-demo-app'
      }
    }
  }
}
