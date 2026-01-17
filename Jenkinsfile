pipeline {
    agent any

    environment {
        VENV = "venv"
        IMAGE_NAME = "abdulahad9049/python-flask-app"
        DOCKER_CREDS = "dockerhub-creds"
        TAG = "latest"
        EC2_HOST = "13.60.40.122"
        EC2_USER = "ubuntu"
        CONTAINER_NAME = "python-flask-app"
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
         stage('Deploy to EC2 via SSH') {
            steps {
                sshagent(credentials: ['ec2-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} \
                    'docker stop ${CONTAINER_NAME} || true && \
                    docker rm ${CONTAINER_NAME} || true && \
                    docker pull ${IMAGE_NAME}:latest && \
                    docker run -d --name ${CONTAINER_NAME} -p 5000:5000 ${IMAGE_NAME}:latest'
                    """
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
