pipeline {
    agent any
    
    environment {
        // Define any environment variables needed for the pipeline
        REGISTRY_CREDENTIALS = credentials('docker-hub-credentials') // Assuming you have Docker Hub credentials set in Jenkins
        KUBE_CONFIG = credentials('kubeconfig') // Assuming you have Kubernetes configuration (kubeconfig) stored in Jenkins
        IMAGE_TAG = "my-app:${env.BUILD_NUMBER}" // Tagging the Docker image with the build number
    }
    
    stages {
        stage('Build') {
            steps {
                script {
                    // Build the Docker image using the provided Dockerfile
                    docker.build("my-app:${env.BUILD_NUMBER}", "-f Dockerfile .")
                }
            }
        }
        
        stage('Push') {
            steps {
                script {
                    // Authenticate with Docker Hub (or your registry) and push the Docker image
                    docker.withRegistry('https://registry.hub.docker.com', REGISTRY_CREDENTIALS) {
                        docker.image("my-app:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    // Deploy to Kubernetes
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh "export KUBECONFIG=${env.KUBECONFIG}"
                        sh "kubectl apply -f serviceaccount.yaml" // Apply the service account first
                        sh "kubectl apply -f kubernetes_service.yaml" // Assuming this is your Kubernetes deployment or service definition
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo 'Deployment successful!'
            // Optionally, you can add more post-success actions here
        }
        
        failure {
            echo 'Deployment failed!'
            // Optionally, you can add more actions to handle failure scenarios
        }
    }
}

