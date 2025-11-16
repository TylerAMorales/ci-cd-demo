pipeline {
    agent any
    environment {
        WEBEX_TOKEN = credentials('webex-token')
        WEBEX_ROOM  = credentials('webex-room')
        PYTHON_BIN = 'python'
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
                    python -m venv .venv
                    . .venv/bin/activate
                    python -m pip install --upgrade pip
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
                    python -m unittest discover -v
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
