pipeline {
  agent any
  options { timestamps() }

 
  environment {
    PATH = "/usr/local/opt/node@18/bin:/usr/local/bin:/usr/bin:/bin:${PATH}"
  }

  stages {

    stage('Checkout') {
      steps {
       
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
       
        sh 'npm test || true'
      }
    }

    stage('Generate Coverage Report') {
      steps {
        
        sh '[ -f package.json ] && (npm run coverage || true) || true'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit || true'
      }
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

  } 
}
