pipeline {
    agent any
    environment {
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
                    if [ ! -d .venv ]; then
                        python -m venv .venv
                    fi
                    . .venv/bin/activate
                    pip install --upgrade pip
                    if [ -f requirements.txt ]; then
                        pip install -r requirements.txt
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
            echo "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
        failure {
            echo "Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
    }
}
