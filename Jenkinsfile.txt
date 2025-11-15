// Jenkinsfile
pipeline {
  agent any

  environment {
    // Set these Jenkins credentials IDs in Jenkins:
    // - webex-token (Secret text) -> your Webex bot token
    // - webex-room (Secret text)  -> the roomId (or spaceId) to post to
    WEBEX_TOKEN = credentials('webex-token')
    WEBEX_ROOM  = credentials('webex-room')
    PYTHON_BIN = 'python3'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Setup & Install') {
      steps {
        sh '''
          set -e
          if [ ! -d .venv ]; then
            python3 -m venv .venv
          fi
          . .venv/bin/activate
          pip install --upgrade pip
          if [ -f requirements.txt ]; then
            pip install -r requirements.txt || true
          fi
        '''
      }
    }

    stage('Run Unit Tests') {
      steps {
        sh '''
          set -e
          . .venv/bin/activate
          # run tests with unittest
          python3 -m unittest discover -v
        '''
      }
    }
  }

  post {
    success {
      script {
        sh """curl -s -X POST \
          -H "Authorization: Bearer ${WEBEX_TOKEN}" \
          -H "Content-Type: application/json" \
          -d '{\"roomId\":\"${WEBEX_ROOM}\",\"text\":\"Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${env.BUILD_URL} (tests passed)\"}' \
          https://webexapis.com/v1/messages"""
      }
    }

    failure {
      script {
        sh """curl -s -X POST \
          -H "Authorization: Bearer ${WEBEX_TOKEN}" \
          -H "Content-Type: application/json" \
          -d '{\"roomId\":\"${WEBEX_ROOM}\",\"text\":\"Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${env.BUILD_URL} (check console)\"}' \
          https://webexapis.com/v1/messages"""
      }
    }

    always {
      script {
        // optional: post a small summary to console
        echo "Build finished: ${currentBuild.currentResult}"
      }
    }
  }
}
