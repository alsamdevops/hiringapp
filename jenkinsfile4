pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_IMAGE = "sabair0509/hiring-app:${BUILD_NUMBER}"
        GIT_REPO = "https://github.com/alsamdevops/Hiring-app-argocd.git"
        MANIFEST_PATH = "dev/deployment.yaml"
    }

    stages {
        stage('Checkout K8S manifest SCM') {
            steps {
                git branch: 'main', credentialsId: 'github-pat', url: "${GIT_REPO}"
            }
        }

        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'github-pat', variable: 'GITHUB_PAT')]) {
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
                            git push https://${GITHUB_PAT}@github.com/alsamdevops/Hiring-app-argocd.git main
                        """
                    }
                }
            }
        }
    }
}

