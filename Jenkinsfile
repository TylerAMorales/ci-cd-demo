pipeline {
    agent {
        docker { image 'python:3.11' }
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
                    python -m venv .venv
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
                    python -m unittest discover -v
                '''
            }
        }
    }
    post {
        success { echo "Build SUCCESS" }
        failure { echo "Build FAILURE" }
    }
}
