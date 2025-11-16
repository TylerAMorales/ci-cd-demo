pipeline {
    agent any
    environment {
        PYTHON_BIN = 'python3' // or just 'python' if Python 3 is in PATH
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
                    $PYTHON_BIN -m venv .venv
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
                    . .venv/bin/activate
                    $PYTHON_BIN -m unittest discover -v
                '''
            }
        }
    }
    post {
        success { echo "Build SUCCESS" }
        failure { echo "Build FAILURE" }
    }
}
