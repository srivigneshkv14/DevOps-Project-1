pipeline {
    agent any

    environment {
        GIT_REPO = 'https://SriVignesh-K-V:%40V%21gnesh098@github.com/SriVignesh-K-V/App.git'
        DOCKER_IMAGE = 'srivigneshkv/guvi_sample_app:latest'
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials' // ID of the Docker Hub credentials in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${env.GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${env.DOCKER_IMAGE}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: "${env.DOCKER_HUB_CREDENTIALS}", url: 'https://index.docker.io/v1/']) {
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
