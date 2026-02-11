pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/TanishqRaj07/todo-app.git'
            }
        }

        stage('Deploy to App Server') {
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
