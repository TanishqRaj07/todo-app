pipeline {
    agent any

    stages {

        // Jenkins automatically checks out the repo
        stage('Build and Deploy') {
            steps {
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
