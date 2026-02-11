pipeline {
    agent any

    stages {
        stage('Build and Deploy') {
            steps {
                // Use the SSH key you just added
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@3.110.40.97 "
                        cd /home/ubuntu &&
                        docker build -t todo-app . &&
                        docker stop todo || true &&
                        docker rm todo || true &&
                        docker run -d -p 5000:5000 --name todo todo-app
                    "
                    '''
                }
            }
        }
    }
}
