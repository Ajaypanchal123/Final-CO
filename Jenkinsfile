pipeline {
    agent any
    
    environment {
        KUBECONFIG = credentials('kubeconfig')
        NAMESPACE = 'atomic-crm'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("atomic-crm:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Deploy to Cluster') {
            steps {
                script {
                    // Apply Kubernetes manifests
                    sh """
                    kubectl apply -f k8s/deployment.yaml -n ${NAMESPACE}
                    kubectl apply -f k8s/service.yaml -n ${NAMESPACE}
                    kubectl apply -f k8s/ingress.yaml -n ${NAMESPACE}
                    """
                    
                    // Wait for rollout
                    sh "kubectl rollout status deployment/atomic-crm -n ${NAMESPACE}"
                }
            }
        }
    }
}
