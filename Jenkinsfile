pipeline {
    agent any
    environment {
        GITHUB_TOKEN = credentials('jenkins-github')  // Ensure this credential ID is correct
        DOCKER_CREDENTIALS_ID = credentials('dockerHubCredentials') // Ensure this credential ID is correct
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
                script {
                    // Build the Docker image
                    sh 'docker build -t rohittrathod/devops:latest .' // Replace with your Docker image name
                }
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubCredentials', 
                                                  usernameVariable: 'DOCKER_USERNAME', 
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        // Log in to Docker Hub
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    sh 'docker push rohittarthod/devops:latest' // Replace with your Docker image name
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes
                    sh '''
                    kubectl apply -f kubernetes/deployment.yaml
                    kubectl apply -f kubernetes/service.yaml
                    kubectl rollout status deployment/devops-deployment
                    '''
                }
            }
        }
        stage('Slack Notification') {
            steps {
                script {
                    // Send a Slack notification
                    slackSend(channel: '#internship', color: 'good', message: "Successfully Completed ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL})")
                }
            }
        }
    }
    post {
        success {
            // Sending success message to Slack channel
            script {
                slackSend(channel: '#internship', color: 'good', message: "Build and deployment succeeded: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
            }
        }
        failure {
            // Sending failure message to Slack channel
            script {
                slackSend(channel: '#internship', color: 'danger', message: "Build and deployment failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
            }
        }
    }
}
