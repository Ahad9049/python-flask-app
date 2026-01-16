pipeline {
    agent any

    environment {
        VENV = "venv"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python Virtual Env') {
            steps {
                sh '''
                python3 -m venv ${VENV}
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                . ${VENV}/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . ${VENV}/bin/activate
                pytest
                '''
            }
        }

    }

    post {
        success {
            echo '✅ Build & Tests Passed'
        }
        failure {
            echo '❌ Build Failed'
        }
    }
}
