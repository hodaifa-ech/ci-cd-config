pipeline {
    agent any
    environment {
        // Set the Argo CD server address
        ARGOCD_SERVER = 'localhost'  // Update this to your Argo CD server
    }
    stages {
        stage('Check Docker') {
            steps {
                // Verify that Docker is available
                sh 'docker --version'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                // Build the Docker image using the Dockerfile in the root directory
                sh 'docker build -t python-app-cicd .'
            }
        }
        
        stage('Push to Artifactory') {
            steps {
                withCredentials([string(credentialsId: 'artifactory-token', variable: 'JFROG_TOKEN')]) {
                    // Log into JFrog Artifactory using the Identity Token
                    sh '''
                        echo $JFROG_TOKEN | docker login perry2011.jfrog.io -u nuperry2011@gmail.com --password-stdin
                    '''
                    
                    // Tag and push the image to JFrog Artifactory
                    sh 'docker tag my-python-app:latest perry2011.jfrog.io/docker-repo/my-python-app:latest'
                    sh 'docker push perry2011.jfrog.io/docker-repo/my-python-app:latest'
                }
            }
        }
        
        stage('Deploy with ArgoCD') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'argocd-credentials', usernameVariable: 'ARGOCD_USER', passwordVariable: 'ARGOCD_PASSWORD')]) {
                    // Login to ArgoCD and sync the application
                    sh '''
                        argocd login $ARGOCD_SERVER --username $ARGOCD_USER --password $ARGOCD_PASSWORD --insecure
                        argocd app sync my-python-app --server $ARGOCD_SERVER
                    '''
                }
            }
        }
    }
}Add Kubernetes Manifests: