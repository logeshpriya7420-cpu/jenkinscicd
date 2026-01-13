pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t jenkins-windows-demo .'
            }
        }

        stage('Run Container') {
            steps {
                bat '''
                docker rm -f demo 2>nul
                docker run -d -p 8000:5000 --name demo jenkins-windows-demo
                '''
            }
        }
    }
}
