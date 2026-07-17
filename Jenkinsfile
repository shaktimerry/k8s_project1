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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                 usernameVariable: 'DOCKER_USER',
                                                 passwordVariable: 'DOCKER_PASS')]) {
                    bat "echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin"
                }
            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE%"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'k8s', variable: 'KUBECONFIG')]) {

                    bat "echo Using kubeconfig: %KUBECONFIG%"
                    bat "kubectl config current-context"

                    // Apply base manifests
                    bat "kubectl apply -f k8s/namespace.yaml"
                    bat "kubectl apply -f k8s/configmap.yaml"
                    bat "kubectl apply -f k8s/secret.yaml"
                    bat "kubectl apply -f k8s/pvc.yaml"
                    bat "kubectl apply -f k8s/deployment.yaml"
                    bat "kubectl apply -f k8s/service.yaml"
                    // bat "kubectl apply -f k8s/hpa.yaml"
                    // bat "kubectl apply -f k8s/vpa.yaml"

                    // Update deployment with the newly built image
                    bat "kubectl set image deployment/nginx-app nginx-app=%IMAGE% -n jenkins-demo"

                    // Wait for rollout
                    bat "kubectl rollout status deployment/nginx-app -n jenkins-demo"
                }
            }
        }
    }

    post {
        success {
            echo 'Image pushed and deployed successfully'

            // Remove local Docker image
            bat "docker rmi %IMAGE% || exit /b 0"
        }

        always {
            echo 'Cleaning Jenkins workspace'
            cleanWs()
        }
    }
}
