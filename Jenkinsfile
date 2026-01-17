pipeline {
    agent any

    environment {
        VENV = "venv"
        IMAGE_NAME = "abdulahad9049/python-flask-app"
        DOCKER_CREDS = "dockerhub-creds"
        TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python Virtual Env') {
            steps {
                sh 'python3 -m venv ${VENV}'
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

        stage('Build Docker Image') {
            steps {
                sh 'docker build --progress=plain -t abdulahad9049/python-flask-app:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDS}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push ${IMAGE_NAME}:${TAG}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD Pipeline Completed Successfully'
        }
        failure {
            echo '❌ Pipeline Failed'
        }
        always {
            sh 'docker logout || true'
        }
    }
}
