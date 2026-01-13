pipeline {
    agent any

    environment {
        IMAGE_NAME = "jenkins-windows-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Run Container') {
            steps {
                bat '''
                docker stop demo || exit 0
                docker rm demo || exit 0
                docker run -d -p 5000:5000 --name demo %IMAGE_NAME%
                '''
            }
        }
    }
}
