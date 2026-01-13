pipeline {
    agent any

    environment {
        IMAGE = "logeswariv/jenkins-demo"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE%:latest .'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    bat '''
                    sonar-scanner ^
                    -Dsonar.projectKey=jenkins-demo ^
                    -Dsonar.sources=. ^
                    -Dsonar.host.url=http://localhost:9000 ^
                    -Dsonar.login=%SONAR_TOKEN%
                    '''
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                bat 'trivy image --severity HIGH,CRITICAL --exit-code 1 %IMAGE%:latest'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                bat 'docker push %IMAGE%:latest'
            }
        }

        stage('Run Container (Local Test)') {
            steps {
                bat '''
                docker rm -f demo 2>nul
                docker run -d -p 8000:5000 --name demo %IMAGE%:latest
                '''
            }
        }
    }
}
