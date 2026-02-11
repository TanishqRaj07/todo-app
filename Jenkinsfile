pipeline {
    agent any

    environment {
        REMOTE_USER = 'ubuntu'
        REMOTE_HOST = '3.110.40.97'
        REPO_URL = 'https://github.com/TanishqRaj07/todo-app.git'
        APP_NAME = 'todo-app'
        CONTAINER_NAME = 'todo'
        REMOTE_DIR = '/home/ubuntu'
    }

    stages {

        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Clone or Update Code on Server') {
            steps {
                sshagent(['ubuntu']) { // 'ubuntu' is the Jenkins credential ID
                    sh """
                    ssh -o StrictHostKeyChecking=no \$REMOTE_USER@\$REMOTE_HOST "
                        cd \$REMOTE_DIR &&
                        if [ -d \$APP_NAME ]; then
                            cd \$APP_NAME && git pull
                        else
                            git clone -b main \$REPO_URL
                            cd \$APP_NAME
                        fi
                    "
                    """
                }
            }
        }

        stage('Ensure Docker Installed') {
            steps {
                sshagent(['ubuntu']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no \$REMOTE_USER@\$REMOTE_HOST "
                        if ! command -v docker &> /dev/null; then
                            echo 'Docker not found. Installing...'
                            sudo apt-get update &&
                            sudo apt-get install -y docker.io &&
                            sudo systemctl enable docker &&
                            sudo systemctl start docker
                        else
                            echo 'Docker already installed'
                        fi
                    "
                    """
                }
            }
        }

        stage('Build and Deploy Docker') {
            steps {
                sshagent(['ubuntu']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no \$REMOTE_USER@\$REMOTE_HOST "
                        cd \$REMOTE_DIR/\$APP_NAME &&
                        docker build -t \$APP_NAME . &&
                        docker stop \$CONTAINER_NAME || true &&
                        docker rm \$CONTAINER_NAME || true &&
                        docker run -d -p 5000:5000 --name \$CONTAINER_NAME \$APP_NAME
                    "
                    """
                }
            }
        }

    }

    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Deployment failed. Check logs!"
        }
    }
}
