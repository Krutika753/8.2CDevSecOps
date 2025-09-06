pipeline {
  agent any
  options { timestamps() }

  // Token comes from Jenkins credentials (ID: SONAR_TOKEN)
  environment {
    SONAR_TOKEN = credentials('SONAR_TOKEN')
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Krutika753/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps { sh 'npm install' }
    }

    stage('Run Tests') {
      steps { sh 'npm test || true' }
    }

    stage('Generate Coverage Report') {
      steps { sh 'npm run coverage || true' }
    }

    stage('NPM Audit (Security Scan)') {
      steps { sh 'npm audit || true' }
    }

    stage('SonarCloud Analysis') {
  steps {
    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
      sh '''
        /usr/local/bin/sonar-scanner \
          -Dsonar.host.url=https://sonarcloud.io \
          -Dsonar.token="$SONAR_TOKEN"
      '''
    }
  }
}
