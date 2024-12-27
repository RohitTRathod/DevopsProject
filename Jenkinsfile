pipeline {
    agent any
    environment {
        GITHUB_TOKEN = credentials('jenkins-github')  // Ensure this credential ID is correct
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                git branch: 'main', url: 'https://github.com/RohitTRathod/DevopsProject.git', credentialsId: 'github-credentials'
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    // Build the Docker image
                    sh 'docker build -t yourapp:latest .' // Use a local tag
                }
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubCredentials', 
                                                  usernameVariable: 'DOCKER_USERNAME', 
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    container('docker') {
                        // Log in to Docker Hub
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                container('docker') {
                    // Push the Docker image to Docker Hub
                    sh 'docker push yourusername/yourapp:latest' // Replace with your Docker image name
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                container('kubectl') {
                    // Deploy to Kubernetes
                    sh '''
                    kubectl apply -f kubernetes/deployment.yaml
                    kubectl apply -f kubernetes/service.yaml
                    kubectl rollout status deployment/yourapp-deployment
                    '''
                }
            }
        }
        stage('Slack Notification') {
            steps {
                slackSend(channel: '#internship', color: 'good', message: "Successfully Completed ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL})")
            }
        }
    }
    post {
        success {
            // Sending success message to Slack channel
            slackSend(channel: '#internship', color: 'good', message: "Build succeeded: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
        }
        failure {
            // Sending failure message to Slack channel
            slackSend(channel: '#internship', color: 'danger', message: "Build failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
        }
    }
}
