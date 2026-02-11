pipeline {
    agent any

    environment {
        REMOTE_USER = 'ubuntu'
        REMOTE_HOST = '3.110.40.97'  // Replace with your EC2 public IP
        APP_NAME = 'todo-app'
        DOCKER_PORT = '5000'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/TanishqRaj07/todo-app.git', branch: 'main'
            }
        }

        stage('Clone or Update Code on Server') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST '
                        if [ -d ~/todo-app ]; then
                            cd ~/todo-app && git pull
                        else
                            git clone https://github.com/TanishqRaj07/todo-app.git ~/todo-app
                        fi
                        '
                    """
                }
            }
        }

        stage('Ensure Docker Installed') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST '
                        if ! command -v docker &> /dev/null; then
                            echo "Docker not found. Installing..."
                            sudo apt-get update && sudo apt-get install -y docker.io
                        else
                            echo "Docker is already installed."
                        fi
                        '
                    """
                }
            }
        }

        stage('Build and Deploy Docker') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST '
                        cd ~/todo-app &&
                        docker build -t $APP_NAME . &&
                        docker stop $APP_NAME || true &&
                        docker rm $APP_NAME || true &&
                        docker run -d -p $DOCKER_PORT:5000 --name $APP_NAME $APP_NAME
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Check logs!'
        }
    }
}
