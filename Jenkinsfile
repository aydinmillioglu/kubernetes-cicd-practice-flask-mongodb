pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = 'dockerhub-cred'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build and Create Image') {
            steps {
                script {
                    env.dockerImageName = 'aydinmillioglu/flask-taskmanager'
                    env.dockerImageTag = "${env.BUILD_NUMBER}"
                    sh "docker build -t ${env.dockerImageName}:${env.dockerImageTag} ."
                }
            }
        }
        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: DOCKER_HUB_CREDENTIALS,
                        usernameVariable: 'DOCKER_HUB_USERNAME',
                        passwordVariable: 'DOCKER_HUB_PASSWORD'
                    )]) {
                        sh "docker login -u ${DOCKER_HUB_USERNAME} -p ${DOCKER_HUB_PASSWORD}"
                        sh "docker push ${env.dockerImageName}:${env.dockerImageTag}"
                    }
                }
            }
        }
        stage('Deploy to k8s Cluster') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: DOCKER_HUB_CREDENTIALS,
                        usernameVariable: 'DOCKER_HUB_USERNAME',
                        passwordVariable: 'DOCKER_HUB_PASSWORD'
                    )]) {
                        sh "sed -i 's/\${VERSION}/${env.dockerImageTag}/' app-cd-deployment.yaml"
                        sh "kubectl apply -f app-cd-deployment.yaml"
                    }
                }
            }
        }
    }
}
