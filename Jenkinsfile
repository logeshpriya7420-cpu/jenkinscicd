pipeline {
    agent any

    environment {
        IMAGE = "azuredevopsfree/jenkins-cicd-demo"
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
                    "C:\\Tools\\sonar scanner\\sonar-scanner-8.0.1.6346-windows-x64\\bin\\sonar-scanner.bat"^
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
                bat '''
                "C:\\Tools\\trivy\\trivy.exe" image ^
                --severity HIGH,CRITICAL ^
                --exit-code 1 ^
                %IMAGE%:latest
                '''
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
