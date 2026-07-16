pipeline {
    agent any

    environment {
        IMAGE = "shaktimerry4/nginx-app:${BUILD_NUMBER}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE% ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                                 usernameVariable: 'DOCKER_USER',
                                                 passwordVariable: 'DOCKER_PASS')]) {
                    bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                }
            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE%"
            }
        }
    }

    post {
        success {
            echo 'Image pushed successfully'

            // Remove local Docker image
            bat "docker rmi %IMAGE% || exit /b 0"
        }

        always {
            echo 'Cleaning Jenkins workspace'
            cleanWs()
        }
    }
}
