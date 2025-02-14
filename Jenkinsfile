pipeline {
    agent any

    environment {
        IMAGE_NAME = "stephenadmin/loadgenerator"
        BUILD_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t ${IMAGE_NAME}:${BUILD_TAG} ."
                    }
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push ${IMAGE_NAME}:${BUILD_TAG}"
                    }
                }
            }
        }
    }
}
