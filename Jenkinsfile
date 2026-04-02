pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "amansumesh/html-demo"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/amansumesh/ISWE406P_Assessment-4.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_IMAGE% ."
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat """
                    echo Logging into DockerHub...
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    docker push %DOCKER_IMAGE%
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(
                    credentialsId: 'kuberconfig',
                    variable: 'KUBECONFIG_FILE'
                )]) {
                    bat """
                    set KUBECONFIG=%KUBECONFIG_FILE%
                    kubectl apply -f deployment.yaml --validate=false
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}