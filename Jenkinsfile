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
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/azizelanbouri/a-reddit-clone-gitops.git'
            }
        }
        stage("Update the Deployment Tags") {
            steps {
                sh """
                    cat k8s/deployment.yaml
                    sed -i 's|azizelanbouri/reddit-clone-pipeline:[^[:space:]]*|azizelanbouri/reddit-clone-pipeline:${IMAGE_TAG}|g' k8s/deployment.yaml
                    cat k8s/deployment.yaml
                """
            }
        }
        stage("Push the changed deployment file to GitHub") {
            steps {
                sh """
                    git config --global user.name "azizelanbouri"
                    git config --global user.email "elanbouriaziz@gmail.com"
                    git add k8s/deployment.yaml
                    git commit -m "Updated Deployment Manifest with tag ${IMAGE_TAG}"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/azizelanbouri/a-reddit-clone-gitops.git main"
                }
            }
        }
        stage("Deploy to Kubernetes") {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh """
                        kubectl apply -f k8s/namespace.yaml
                        kubectl apply -f k8s/deployment.yaml
                        kubectl apply -f k8s/service.yaml
                    """
                }
            }
        }
    }
}