pipeline {
    agent any
    environment {
        IMAGE_NAME = "stephenadmin/adservice"
        BUILD_TAG = "${BUILD_NUMBER}"
        DEPLOYMENT_MANIFEST = "deployment-manifest.yml"
        REPO_URL = "https://github.com/CloudHight/set1-microserviceapp.git" // Replace with your actual public GitHub repo URL
        STAGE_BRANCH = "stage"
        MAIN_BRANCH = "main"
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
        stage('Update Deployment Manifest') {
            steps {
                script {
                    sh """
                        sed -i 's|image: ${IMAGE_NAME}:.*|image: ${IMAGE_NAME}:${BUILD_TAG}|' ${DEPLOYMENT_MANIFEST}
                    """
                }
            }
        }
        stage('Commit & Push Changes to Stage Branch') {
            steps {
                script {
                    sh """
                        git config --global user.email "jenkins@eamanzetec.com.ng"
                        git config --global user.name "Jenkins CI"
                        git checkout ${STAGE_BRANCH}
                        git add ${DEPLOYMENT_MANIFEST}
                        git commit -m "Update image tag to ${IMAGE_NAME}:${BUILD_TAG}"
                        git push ${REPO_URL} ${STAGE_BRANCH}
                    """
                }
            }
        }
        stage('Manual Approval') {
            steps {
                input message: 'Approve deployment to the main branch?', ok: 'Deploy'
            }
        }
        stage('Update & Commit to Main Branch') {
            steps {
                script {
                    sh """
                        git checkout ${MAIN_BRANCH}
                        git merge ${STAGE_BRANCH}
                        git push ${REPO_URL} ${MAIN_BRANCH}
                    """
                }
            }
        }
    }
}
