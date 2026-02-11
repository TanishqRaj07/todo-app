pipeline {
    agent any
    environment {
        REMOTE_USER = 'ubuntu'                  // EC2 user
        REMOTE_HOST = '3.110.40.97'            // Your EC2 public IP
        APP_NAME = 'todo-app'                   // Docker container name
        DOCKER_PORT = '5000'                    // Port to expose
    }
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('Build & Deploy on EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST '
                            # Navigate or clone repo
                            if [ ! -d "$APP_NAME" ]; then
                                git clone https://github.com/TanishqRaj07/todo-app.git $APP_NAME
                            fi
                            cd $APP_NAME
                            git pull

                            # Build Docker image
                            docker build -t $APP_NAME .

                            # Stop & remove old container if exists
                            docker stop $APP_NAME || true
                            docker rm $APP_NAME || true

                            # Run new container
                            docker run -d -p $DOCKER_PORT:5000 --name $APP_NAME $APP_NAME
                        '
                    """
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed. Check logs!'
        }
    }
}
