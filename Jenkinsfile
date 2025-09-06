pipeline {
  agent any
  environment {
    'PATH+NODE' = '/usr/local/opt/node@18/bin'     
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
      steps { sh 'npm test || true' }              // continue even if tests fail
    }

    stage('Generate Coverage Report') {
      steps { sh 'npm run coverage || true' }      // ok if no coverage script exists
    }

    stage('NPM Audit (Security Scan)') {
      steps { sh 'npm audit || true' }
    }

    stage('SonarCloud Analysis') {
      steps {
        sh '''
          set -e
          VER=5.0.1.3006
          case "$(uname -s)" in
            Darwin) SUFFIX="macosx" ;;             # your Jenkins host is macOS
            Linux)  SUFFIX="linux"  ;;
            *) echo "Unsupported OS"; exit 1 ;;
          esac

          rm -rf .scanner
          mkdir -p .scanner
          curl -fsSL -o .scanner/sonar.zip \
            "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-${VER}-${SUFFIX}.zip"
          unzip -qo .scanner/sonar.zip -d .scanner
          SCAN_BIN="$(echo .scanner/sonar-scanner-*/bin/sonar-scanner)"

          # Run scanner; token comes from Jenkins credentials
          "$SCAN_BIN" -Dsonar.token="$SONAR_TOKEN"
        '''
      }
    }
  }
}
