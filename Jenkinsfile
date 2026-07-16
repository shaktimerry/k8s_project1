pipeline {

    agent any

    environment {

        IMAGE_NAME = "shaktimerry4/hello-nginx2"
        IMAGE_TAG = "${BUILD_NUMBER}"

        DOCKERHUB_CREDENTIALS = "dockerhub"

        CONTAINER_NAME = "hello-nginx-test"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shaktimerry/k8s_project1.git'
            }
        }

        stage('Build Docker Image') {
            steps {

                bat """
                docker build -t %IMAGE_NAME%:%IMAGE_TAG% .
                """

            }
        }

        stage('Run Container Test') {

            steps {

                bat """
                docker rm -f %CONTAINER_NAME% || exit 0
                docker run -d --name %CONTAINER_NAME% -p 8080:80 %IMAGE_NAME%:%IMAGE_TAG%
                timeout /t 10
                curl http://localhost:8080
                docker rm -f %CONTAINER_NAME%
                """

            }
        }

        stage('Docker Login') {

            steps {

                withCredentials([usernamePassword(
                    credentialsId: "${DOCKERHUB_CREDENTIALS}",
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD')]) {

                    bat """
                    echo %PASSWORD% | docker login -u %USERNAME% --password-stdin
                    """

                }

            }

        }

        stage('Push Image') {

            steps {

                bat """
                docker push %IMAGE_NAME%:%IMAGE_TAG%
                docker tag %IMAGE_NAME%:%IMAGE_TAG% %IMAGE_NAME%:latest
                docker push %IMAGE_NAME%:latest
                """

            }

        }

        stage('Deploy to Kubernetes') {

            steps {

                bat """
                kubectl set image deployment/hello-nginx hello-nginx=%IMAGE_NAME%:%IMAGE_TAG%
                kubectl rollout status deployment/hello-nginx
                """

            }

        }

    }

    post {

        success {

            echo 'Deployment Successful'

        }

        failure {

            echo 'Deployment Failed'

        }

        always {

            bat "docker image prune -f"

        }

    }

}
