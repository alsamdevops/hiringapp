pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_IMAGE = "aslamdevops/hiring-app:${BUILD_NUMBER}"
        GIT_REPO = "git@github.com:alsamdevops/Hiring-app-argocd.git"
        MANIFEST_PATH = "dev/deployment.yaml"
    }

    stages {

        stage('Docker Build') {
            steps {
                sh "docker build . -t ${DOCKER_IMAGE}"
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub', variable: 'hubPwd')]) {
                    sh """
                        echo ${hubPwd} | docker login -u aslamdevops --password-stdin
                        docker push ${DOCKER_IMAGE}
                    """
                }
            }
        }

        stage('Checkout K8S manifest SCM') {
            steps {
                git branch: 'main', credentialsId: 'github-ssh', url: "${GIT_REPO}"
            }
        }

        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    sh """
                        echo 'Before update:'
                        cat ${MANIFEST_PATH}

                        sed -i "s|image: .*/hiring-app:.*|image: ${DOCKER_IMAGE}|g" ${MANIFEST_PATH}

                        echo 'After update:'
                        cat ${MANIFEST_PATH}

                        git config user.email "jenkins@pipeline.com"
                        git config user.name "Jenkins CI"

                        git add ${MANIFEST_PATH}
                        git commit -m 'Updated deploy yaml | Jenkins Pipeline' || echo "No changes to commit"
                        git push origin main
                    """
                }
            }
        }
    }
}

