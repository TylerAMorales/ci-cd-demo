pipeline {
    agent {
        docker { image 'python:3.11' } // Python is pre-installed in this container
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/TylerAMorales/ci-cd-demo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'python -m unittest discover -s . -p "test_*.py"'
            }
        }
    }
}
