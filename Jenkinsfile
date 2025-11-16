pipeline {
    agent any
    environment {
        APP_NAME = "reddit-clone-pipeline"
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/azizelanbouri/a-reddit-clone-gitops'
            }
        }
        stage("Update the Deployment Tags") {
            steps {
                sh """
                    cat deployment.yaml
                    sed -i 's|azizelanbouri/reddit-clone-pipeline:[^[:space:]]*|azizelanbouri/reddit-clone-pipeline:${params.IMAGE_TAG}|g' deployment.yaml
                    cat deployment.yaml
                """
            }
        }
        stage("Push the changed deployment file to GitHub") {
            steps {
                sh """
                    git config --global user.name "azizelanbouri"
                    git config --global user.email "elanbouriaziz@gmail.com"
                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest with tag ${params.IMAGE_TAG}"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/azizelanbouri/a-reddit-clone-gitops main"
                }
            }
        }
    }
}
