pipeline {
    agent any
    environment {
        WEBEX_TOKEN = credentials('webex-token')
        WEBEX_ROOM  = credentials('webex-room')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Setup & Install') {
            steps {
                bat """
                python -m venv .venv
                .\\.venv\\Scripts\\activate
                pip install --upgrade pip
                pip install -r requirements.txt
                """
            }
        }
        stage('Run Unit Tests') {
            steps {
                bat """
                .\\.venv\\Scripts\\activate
                python -m unittest discover -v
                """
            }
        }
    }
    post {
        success {
            script {
                bat """
                curl -s -X POST -H "Authorization: Bearer ${WEBEX_TOKEN}" -H "Content-Type: application/json" ^
                -d "{\\"roomId\\":\\"${WEBEX_ROOM}\\",\\"text\\":\\"Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${env.BUILD_URL} (tests passed)\\"}" ^
                https://webexapis.com/v1/messages
                """
            }
        }
        failure {
            script {
                bat """
                curl -s -X POST -H "Authorization: Bearer ${WEBEX_TOKEN}" -H "Content-Type: application/json" ^
                -d "{\\"roomId\\":\\"${WEBEX_ROOM}\\",\\"text\\":\\"Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${env.BUILD_URL} (check console)\\"}" ^
                https://webexapis.com/v1/messages
                """
            }
        }
    }
}
