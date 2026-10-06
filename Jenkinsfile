pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Helm Dependencies') {
            steps {
                sh '''
                    helm repo add openproject https://charts.openproject.org/
                    helm repo update
                    helm dependency build ./helm
                '''
            }
        }

        stage('Helm Lint') {
            steps {
                sh '''
                    helm lint ./helm -f ./helm/values.yaml
                '''
            }
        }

        stage('Dry-Run Template Validation') {
            steps {
                sh '''
                    helm template openproject ./helm -f ./helm/values.yaml > /dev/null
                    echo "Helm template dry-run rendered successfully!"
                '''
            }
        }
    }

    post {
        success {
            echo "CI validation passed. ArgoCD will handle deployment synchronization."
        }
        failure {
            echo "CI checks failed. Please fix Helm errors before merging."
        }
    }
}
