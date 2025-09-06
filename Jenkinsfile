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
        sh '''
          set -e
          VER=5.0.1.3006
          case "$(uname -s)" in
            Darwin) SUFFIX="macosx" ;;   # macOS Jenkins host
            *)      SUFFIX="linux"  ;;
          esac

          rm -rf .scanner
          mkdir -p .scanner
          curl -fsSL -o .scanner/sonar.zip \
            "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-${VER}-${SUFFIX}.zip"
          unzip -qo .scanner/sonar.zip -d .scanner
          SCAN_BIN="$(echo .scanner/sonar-scanner-*/bin/sonar-scanner)"

          # Use the injected credential
          "$SCAN_BIN" -Dsonar.token="$SONAR_TOKEN"
        '''
      }
    }
  }
}
